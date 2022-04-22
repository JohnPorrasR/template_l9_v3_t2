<template>
    <aside
        class="main-menu">
        <!--Logo-->
        <div
            class="header"
            :class="[
        mainMenuConf.logoAreaClasses ? mainMenuConf.logoAreaClasses : appConf.logoAreaClasses,
        `radius-${mainMenuConf.logoAreaRadius ? mainMenuConf.logoAreaRadius : appConf.radius}`
        ]"
        >
            <Link
                :href="route('/')"
                class="logo-out-container"
            >
                <div
                    class="logo-inside-container"
                    :class="foldMainMenu && 'ml-1'"
                >
                    <!--TODO: Title and Logo will come from DB-->
                    <!--Logo-->
                    <img
                        :src="[
                        appearingMode === 'dark' ? mainMenuConf.logo.dark ? mainMenuConf.logo.dark : appConf.logo.dark :
                        mainMenuConf.logo.light ? mainMenuConf.logo.light : appConf.logo.light
                    ]"
                        :class="mainMenuConf.logoClasses"
                    />
                    <!--Title-->
                    <div
                        id="logo-title"
                        v-html="mainMenuConf.appName ? mainMenuConf.appName : appConf.appName"
                        :class="[
                            !foldMainMenu ? 'main-menu-title-show' : 'main-menu-title-hide',
                            mainMenuConf.appNameClasses
                            ]"
                    />
                </div>
            </Link>
        </div>
        <!--Menu Items-->
        <nav id="links-wrapper">
            <slot/>
        </nav>
    </aside>
</template>

<script>
/*Main Functions*/
import {defineComponent, inject, provide} from "vue";
import {Link} from "@inertiajs/inertia-vue3";

/*Sources*/
import MainMenuLinks from "@/Sources/mainMenuLinks";
import {appConf, mainMenuConf} from "@/config";

/*Multi Language*/
import {Inertia} from "@inertiajs/inertia";

export default defineComponent({
    name: "MainMenu",
    components: {
        Link
    },
    emits: ["updateMainMenuStatus"],
    setup() {
        /*Menu*/
        const {mainMenuFooterLinks} = MainMenuLinks(Inertia.page.props)


        /*Injection*/
        const foldMainMenu = inject("foldMainMenu");
        const appearingMode = inject("appearingMode");

        return {
            appConf,
            appearingMode,
            mainMenuConf,
            foldMainMenu,
            mainMenuFooterLinks,
        };
    }
});
</script>

<style scoped>
.fade-enter-active, .fade-leave-active {
    transition: all 300ms;
}

.fade-enter-from, .fade-leave-to {
    opacity: 0;
    transform: scale(0);
}

.fade-enter-to, .fade-leave-from {
    opacity: 1;
    transform: scale(1);
}
</style>
