<script setup>
import localforage from "localforage"
import { useRouter } from "vue-router";
import { useCollapseStateStore } from "@/stores/data/collapseStateStore";
import { useNavStore } from "@/stores/data/navStore";
import { useUserInfoStore } from "@/stores/data/userInfoStore";
import { computed, onMounted } from "vue";
import loginUtil from "@/utils/LoginUtil";
import screenUtil from "@/utils/ScreenUtil";
import { appWindow, LogicalSize } from '@tauri-apps/api/window';
import { message } from '@tauri-apps/api/dialog';


const router = useRouter()
const navStore = useNavStore()
const userInfoStore = useUserInfoStore()
const collapseStateStore = useCollapseStateStore()

onMounted(() => {
    // 获取用户信息
    localforage.getItem("userInfo").then((val) => {
        userInfoStore.setUserInfo(val)
    })
})

// 菜单折叠状态
const collapseState = computed(() => collapseStateStore.collapseState)

// 面包屑
const breadcrumb = computed(() => {
    let routeInfo = navStore.routeInfo
    return routeInfo.breadcrumb
})

// 用户名称
const userNick = computed(() => userInfoStore.userInfo.userNick)

/**
 * 菜单折叠按钮点击事件
 */
const collapseClick = () => {
    collapseStateStore.setCollapseState(!collapseState.value)
}

/**
 * 个人中心
 */
const userInfoClick = () => {
    router.push("/user/userInfo")
}

/**
 * 消息中心
 */
const userMessageClick = () => {
    router.push("/user/message")
}

/**
 * 全屏
 */
const screenFull = () => {
    screenUtil.screenFull(val => { })
}

/**
 * 退出登录
 */
const logout = () => {
    loginUtil.logout()
    router.push("/login")
}

/**
 * 切换主题
 */
const setTheme = () => {
    message('功能还在开发中', { title: 'RapidTools', type: 'info' })
}

/**
 * 程序最小化
 */
const windowMin = async () => {
    await appWindow.minimize();
}

/**
 * 程序最大化
 */
const windowMax = async () => {
    let max = await appWindow.isMaximized();
    console.log(max)
    if (max) {
        appWindow.setSize(new LogicalSize(1000, 800));
    } else {
        await appWindow.maximize();
    }
}

/**
 * 关闭程序
 */
const windowClose = () => {
    appWindow.hide();
}
</script>

<script>
import {
    checkUpdate,
    onUpdaterEvent,
} from '@tauri-apps/api/updater'


onUpdaterEvent(res => {
    console.log(res)
})



function updater(){
    console.log("升级程序");
    checkUpdate().then(res => {
        console.log(res)
    })
}

</script>

<template>
    <section class="vel_container_header_menu" data-tauri-drag-region>
        <div class="vel_container_header_menu_left">
            <div @click="collapseClick" class="vel_header_item vel_container_header_menu_left_collapse">
                <el-icon :size="18">
                    <component :is="collapseState ? 'Expand' : 'Fold'" />
                </el-icon>
            </div>
            <!--<div class="vel_header_item vel_container_header_menu_left_breadcrumb">
                <el-breadcrumb separator="/">
                    <el-breadcrumb-item
                        v-for="(crumb, index) of breadcrumb"
                        :key="index">
                        {{ crumb }}
                    </el-breadcrumb-item>
                </el-breadcrumb>
            </div>-->
        </div>

        <div class="vel_container_header_menu_right">
            <div class="vel_header_item vel_container_header_menu_right_nick">
                
                <!--<el-dropdown class="el_dropdown_override margin_left">
                    <el-button type="primary" text size="small" @click="updater">
                        <el-icon color="#409EFC" class="no-inherit">
                            <Upload />
                        </el-icon>
                    </el-button>
                </el-dropdown>
                <el-dropdown class="el_dropdown_override margin_left">
                    <el-button type="primary" text size="small" @click="setTheme">
                        <el-icon color="#409EFC" class="no-inherit">
                            <Sunny />
                        </el-icon>
                    </el-button>
                </el-dropdown>-->
                
                <el-dropdown class="el_dropdown_override margin_left">
                    <el-button type="primary" text size="small" @click="windowMin">
                        <el-icon color="#409EFC" class="no-inherit">
                            <Minus />
                        </el-icon>
                    </el-button>
                </el-dropdown>
                <el-dropdown class="el_dropdown_override margin_left">
                    <el-button type="primary" text size="small" @click="windowMax">
                        <el-icon color="#E6A23C" class="no-inherit">
                            <CopyDocument />
                        </el-icon>
                    </el-button>
                </el-dropdown>
                <el-dropdown class="el_dropdown_override margin_left">
                    <el-button type="primary" text size="small" @click="windowClose">
                        <el-icon color="#F56C6C" class="no-inherit">
                            <CloseBold />
                        </el-icon>
                    </el-button>
                </el-dropdown>
            </div>
        </div>
        <!--<div class="vel_container_header_menu_right">
            <div class="vel_header_item vel_container_header_menu_right_nick">
                <el-dropdown class="el_dropdown_override">
                <span>
                    <el-badge
                        class="el_badge_override"
                        is-dot>
                        <el-avatar
                            class="el_avatar_override"
                            :size="30">
                            <el-icon :size="24"><ElementPlus/></el-icon>
                        </el-avatar>
                    </el-badge>
                    <span>{{ userNick }}</span>
                </span>
                    <template #dropdown>
                        <el-dropdown-menu class="el_dropdown_menu_override">
                            <el-dropdown-item @click="userInfoClick">
                                <el-icon :size="18">
                                    <User/>
                                </el-icon>
                                个人中心
                            </el-dropdown-item>
                            <el-dropdown-item @click="userMessageClick">
                                <el-icon :size="18">
                                    <Bell/>
                                </el-icon>
                                我的消息
                                <el-badge value="new"/>
                            </el-dropdown-item>
                            <el-dropdown-item
                                @click="logout"
                                divided>
                                <el-icon :size="18">
                                    <SwitchButton/>
                                </el-icon>
                                退出
                            </el-dropdown-item>
                        </el-dropdown-menu>
                    </template>
                </el-dropdown>
            </div>
            <div
                @click="screenFull"
                class="vel_header_item vel_container_header_menu_right_fullscreen">
                <el-icon :size="18">
                    <FullScreen/>
                </el-icon>
            </div>
        </div>-->
    </section>
</template>

<style scoped>
.margin_left {
    margin-left: 12px;
}

.vel_container_header_menu {
    height: 50px;
    border-bottom: 1px solid #f4f4f4;
    border-top: 1px solid #f4f4f4;
    box-sizing: border-box;
    display: flex;
    align-items: center;
    justify-content: space-between;
}

.vel_container_header_menu_left,
.vel_container_header_menu_right {
    display: flex;
    align-items: center;
}

.vel_header_item {
    height: 50px;
    padding: 0 10px;
    display: flex;
    align-items: center;
    transition: background-color .2s;
}

.vel_header_item:not(.vel_container_header_menu_left_breadcrumb, .vel_container_header_menu_right_nick):hover {
    background-color: #F2F6FC;
    cursor: pointer;
}

:deep(.el_badge_override) sup.is-dot {
    right: 8px;
    top: 4px
}

.el_badge_override {
    margin-right: 5px;
}

.el_avatar_override {
    background-color: #409eff;
}

:deep(li).el-dropdown-menu__item {
    padding: 10px 15px;
    min-width: 120px;
    color: #7c7c7c;
}

:deep(li).el-dropdown-menu__item:focus {
    color: #303133;
    background-color: #F2F6FC;
}

:deep(li).el-dropdown-menu__item .el-badge {
    display: flex;
    align-items: center;
    margin-left: 5px;
}
</style>