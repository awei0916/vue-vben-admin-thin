<script lang="tsx">
  import { defineComponent, unref, computed } from 'compatible-vue';
  import { Layout, Tooltip } from 'ant-design-vue';
  import { Icon } from '@/components/icon/index';
  import UserDropdown from './UserDropdown.vue';
  import Logo from '@/layouts/Logo.vue';
  import LayoutBread from './LayoutBread.vue';

  import LockAction from './actions/LockActionItem.vue';
  import { useModal } from '@/components/modal/index';

  import LayoutMenu from './LayoutMenu.vue';
  import { appStore } from '@/store/modules/app';
  import { MenuModeEnum, MenuTypeEnum } from '@/enums/menuEnum';

  // hooks
  import { useDesign } from '@/hooks/core/useDesign';
  import { useTabs } from '@/hooks/functions/useTabs';
  import { useFullscreen } from '@/hooks/event/useFullScreen';

  export default defineComponent({
    name: 'DefaultLayoutHeader',
    setup() {
      const { prefixCls } = useDesign('layout-header');
      const { toggleFullscreen, isFullscreenRef } = useFullscreen();

      const [register, { openModal }] = useModal();
      const { refreshPage } = useTabs();
      const headerClass = computed(() => {
        const theme = appStore.projCfgState!.headerSetting.theme;
        return theme ? `${prefixCls}__header--${theme}` : '';
      });

      /**
       * @description: 锁定屏幕
       */
      function handleLockPage() {
        openModal({
          visible: true,
        });
      }

      return () => {
        const { getProjCfg } = appStore;
        const {
          // showGithubButton,
          showLogo,
          headerSetting: { theme: headerTheme, useLockPage, showRedo, showFullScreen } = {},
          menuSetting: { mode, type: menuType } = {},
          showBreadCrumb,
        } = getProjCfg;
        const isSidebarType = menuType !== MenuTypeEnum.SIDEBAR;

        return (
          <Layout.Header class={[prefixCls, unref(headerClass)]}>
            {
              // <div class={`${prefixCls}-lm`}>
            }
            {showLogo && isSidebarType ? (
              <Logo class={`${prefixCls}__logo`} />
            ) : (
              <span>&nbsp;</span>
            )}

            {mode === MenuModeEnum.HORIZONTAL && (
              <div class={`${prefixCls}__menu`}>
                <LayoutMenu theme={headerTheme} />
              </div>
            )}

            {mode !== MenuModeEnum.HORIZONTAL && showBreadCrumb && (
              <LayoutBread class={`${prefixCls}__bread`} />
            )}

            <div class={`${prefixCls}__action`}>
              {showRedo && (
                <Tooltip>
                  <template slot="title">刷新</template>
                  <div
                    class={`${prefixCls}__action-item`}
                    onClick={refreshPage}
                    id="elem-driver-action-refresh"
                  >
                    <Icon type="redo" class={`${prefixCls}__action-icon`} />
                  </div>
                </Tooltip>
              )}

              {useLockPage && (
                <Tooltip>
                  <template slot="title">锁定屏幕</template>
                  <div class={`${prefixCls}__action-item`} onClick={handleLockPage}>
                    <Icon type="lock" class={`${prefixCls}__action-icon`} />
                  </div>
                </Tooltip>
              )}

              {showFullScreen && (
                <Tooltip>
                  <template slot="title">{unref(isFullscreenRef) ? '退出全屏' : '全屏'}</template>
                  <div class={`${prefixCls}__action-item`} onClick={toggleFullscreen}>
                    <Icon
                      type={!unref(isFullscreenRef) ? 'fullscreen' : 'fullscreen-exit'}
                      class={`${prefixCls}__action-icon`}
                    />
                  </div>
                </Tooltip>
              )}

              <UserDropdown class={`${prefixCls}__user-dropdown`} />
            </div>
            <LockAction onRegister={register} />
          </Layout.Header>
        );
      };
    },
  });
</script>
<style lang="less">
  @import './layout-header.less';
</style>
