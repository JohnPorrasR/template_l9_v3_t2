<template>
    <t-login
        color="solid-gray"
        bg-color="gradient-gray-to"
        :radius="5"
        login-button-color="gray"
        login-buttondesign="light"
        register-button-color="white"
        register-button-design="outline"
        :canResetPassword="canResetPassword"
        :status="status"
    >
    </t-login>
</template>

<script>

/*Main functions*/
import {defineComponent} from "vue";
import { Head } from "@inertiajs/inertia-vue3";

/*Components*/
import TLogin from "@/Components/Auth/TLogin";

/* Multi language */
import {useI18n} from "vue-i18n";
import {authTranslates} from "@/Lang/languages";

export default defineComponent({
    props: {
        canResetPassword: Boolean,
        status: String,
    },
    components: {
        TLogin,
        Head
    },
    setup(){
        const {t,tm} = useI18n({
            inheritLocale: true,
            messages: authTranslates
        })

        return {t, tm}
    }
})
</script>
