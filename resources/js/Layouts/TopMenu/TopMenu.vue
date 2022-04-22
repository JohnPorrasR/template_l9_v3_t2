<template>
    <div
        class="top-menu"
        :class="`radius-${topBarConf.radius ? topBarConf.radius : appConf.radius}`"
    >
        <!--Left Menu Trigger-->
        <div
            @click="$emit('updateMainMenuStatus', $event)"
            class="trigger"
            :class="`radius-${topBarConf.radius ? topBarConf.radius : appConf.radius}`"
        >
            <!--Fold & Close Icon-->
            <svg
                class="trigger-icon"
                :class="[
                (showMainMenu && !foldMainMenu) || ((deviceType === 'phone' || deviceType==='tablet')&&showMainMenu) ?
                'trigger-icon-show' : 'trigger-icon-hide'
                ]"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
            >
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
            </svg>
            <!--Open Icon-->
            <svg
                class="trigger-icon"
                :class="[
                (showMainMenu && foldMainMenu) || !showMainMenu ?
                'trigger-icon-show' : 'trigger-icon-hide'
                ]"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
            >
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16"/>
            </svg>
        </div>

        <!--User Menu-->
        <top-menu-user-menu/>
    </div>
    <teleport to="body">
        <!--Search Bar-->
        <t-loading v-model="searchBar" color="gray" title="Search" closeable>
            <div
                class="search-modal"
                :class="`radius-${topBarConf.radius ? topBarConf.radius : appConf.radius}`"
            >
                <input type="text" id="search" :placeholder="topBarConf.searchPlaceHolderText"/>
            </div>
        </t-loading>
    </teleport>
</template>

<script>
/*Main functions*/
import TopMenuNotification from "@/Layouts/TopMenu/TopMenuNotification";
import TopMenuUserMenu from "@/Layouts/TopMenu/TopMenuUserMenu";
import {defineComponent, inject, provide, ref} from "vue";
import TLoading from "@/Components/Loading/TLoading";
import TopMenuThemeSelector from "@/Layouts/TopMenu/TopMenuThemeSelector";
import TopMenuLanguageSelector from "@/Layouts/TopMenu/TopMenuLanguageSelector";

/*Sources*/
import {appConf, topBarConf} from "@/config";

export default defineComponent({
    name: "TopMenu",
    components: {TopMenuLanguageSelector, TopMenuThemeSelector, TLoading, TopMenuUserMenu, TopMenuNotification},
    emits: ["updateMainMenuStatus"],
    setup() {
        /*Definitions*/
        const searchBar = ref(false);
        /*Injections*/
        const deviceType = inject("deviceType");
        const foldMainMenu = inject("foldMainMenu");
        const showMainMenu = inject("showMainMenu");

        /*Provider*/
        provide('appConf', appConf);
        provide('topBarConf', topBarConf);

        return {appConf, topBarConf, deviceType, foldMainMenu, showMainMenu, searchBar};
    }
});
</script>

<style scoped>

</style>
