<template>
    <full-screen-layout
        :bg-image-url="activeDesign.bgImage[appearingMode]"
        :bg-color="activeDesign.bgColor"
    >

        <!--Container-->
        <div :class="[
                'auth-container',
                {'w-full' : deviceType === 'phone'},
            ]">
            <!--Header-->
            <div
                class="auth-header"
                :class="[
                        activeDesign.header,
                        deviceType !== 'phone' && `radius-t-${activeDesign.radius ? activeDesign.radius : appConf.radius}`,
                    ]"
            >
                <!--Greeting-->
                <div class="auth-greeting" v-if="status || $slots.greeting">
                    <div class="text-sm">
                        <!--Status-->
                        <div v-if="status" class="auth-status">{{ status }}</div>
                        <slot v-else name="greeting"/>
                    </div>
                </div>
            </div>

            <!--Form-->
            <div
                class="auth-form"
                :class="[
                        deviceType !== 'phone' && `radius-b-${activeDesign.radius ? activeDesign.radius : appConf.radius}`,
                        activeDesign.body,
                    ]"
            >
                <form @submit.prevent="submit">
                    <!--Email-->
                    <div>
                        <t-input-group
                            :label="t('Usuario')"
                            label-for="name"
                            :errors="v.name.$errors"
                        >
                            <t-input-text
                                id="name"
                                v-model="form.name"
                                @blur="v.name.$touch"
                                :radius="3"
                                autofocus
                                autocomplete="username"
                                required
                                type="text"
                            />
                        </t-input-group>
                    </div>
                    <!--Password-->
                    <div class="mt-4">
                        <t-input-group
                            :label="t('Clave')"
                            label-for="password"
                            :errors="v.password.$errors"
                        >
                            <t-input-text
                                id="password"
                                v-model="form.password"
                                @blur="v.password.$touch"
                                :radius="3"
                                autocomplete="current-password"
                                required
                                type="password"
                            />
                        </t-input-group>
                    </div>

                    <!--Submit Area-->
                    <div class="auth-submit-area">
                        <!--Submit Button-->
                        <t-button
                            :class="{ 'opacity-25': form.processing }"
                            :color="activeDesign.loginButton[appearingMode].color"
                            :design="activeDesign.loginButton[appearingMode].design"
                            :disabled="form.processing"
                            :radius="3"
                            class="ml-4"
                        >{{ t('login') }}
                        </t-button>
                    </div>
                </form>
            </div>
        </div>

        <!--Errors-->
        <div class="auth-error">
            <transition @before-enter="beforeStyle" @after-enter="enterStyle">
                <t-alert v-if="hasErrors" :radius="deviceType !== 'phone' && 5" color="danger">
                    <template #icon>
                        <icon icon="bell" size="lg"/>
                    </template>
                    <ul class="list-inside text-sm">
                        <li v-for="(error, key) in errors" :key="key">{{ error }}</li>
                    </ul>
                </t-alert>
            </transition>
        </div>

    </full-screen-layout>
</template>

<script>
/*Main functions*/
import {defineComponent, computed, ref} from "vue";
import {loginStyleMixin} from "@/Mixins/Styles/loginStyleMixin";
import {Link, useForm} from "@inertiajs/inertia-vue3";
import windowSizeCalculator from "@/Functions/windowSizeCalculator";
import darkModeFn from "@/Functions/darkMode";
import useVuelidate from "@vuelidate/core";
import {email, helpers, required} from "@vuelidate/validators";

/*Components*/
import TAlert from "@/Components/Alert/TAlert";
import TBellIcon from "@/Components/Icon/TBellIcon";
import TButton from "@/Components/Button/TButton";
import FullScreenLayout from "@/Layouts/FullScreenLayout";
import TInputGroup from "@/Components/Form/TInputGroup";
import TInputText from "@/Components/Form/Inputs/TInputText";
import TDropdown from "@/Components/Dropdown/TDropdown";
import TTooltip from "@/Components/Tooltip/TTooltip";
import TInputCheckBox from "@/Components/Form/Inputs/TInputCheckBox";

/*Sources*/
import {appConf, authScreenConf, badgeConf} from "@/config";
import {authDesigns} from "@/Sources/authScreenDesigns";

/* Multi language */
import {useI18n} from "vue-i18n";
import langChooserFn from "@/Functions/langChooser"
import {languages, flags, authTranslates} from "@/Lang/languages";

/*Fontawesome icons*/
import {library} from "@fortawesome/fontawesome-svg-core";
import {faSun, faMoon, faPalette, faRedo, faKey, faBell} from "@fortawesome/free-solid-svg-icons";

library.add(faSun, faMoon, faPalette, faRedo, faKey, faBell)

export default defineComponent({
    name: "TLogin",
    components: {
        TInputCheckBox,
        TTooltip,
        TAlert,
        TBellIcon,
        TButton,
        TDropdown,
        FullScreenLayout,
        TInputGroup,
        TInputText,
        Link,
        ...flags,
    },
    mixins: [loginStyleMixin],
    props: {
        canResetPassword: Boolean,
        status: String,
    },
    setup(props) {
        /*Device type*/
        const {deviceType} = windowSizeCalculator();

        /* Dark Mode */
        const {darkMode, appearingMode, changeTheme} = darkModeFn();

        /* Multi-language */
        const {changeLang, locale} = langChooserFn();

        const {t, tm} = useI18n({
            inheritLocale: true,
            messages: authTranslates,
        });

        /* Login */
        const form = useForm({
            name: "",
            password: "",
            remember: false,
        });


        /* Client-side Validation */
        const rules = ref({
            name: {
                required: helpers.withMessage(tm('validationMessage.email.required'), required),
            },
            password: {
                required: helpers.withMessage(tm('validationMessage.password.required'), required)
            },
        });
        const v = useVuelidate(rules, form, {$lazy: true});

        /*Submit*/
        const submit = async () => {
            if (!(await v.value.$validate())) {
                return;
            }
            form
                .transform((data) => ({
                    ...data,
                    remember: form.remember ? "on" : "",
                }))
                .post(route("login"), {
                    onFinish: () => form.reset("password"),
                });
        };

        /*Logo SRC*/
        /*Temporary Definitions*/
        const temporaryLogo = computed(()=>{
            let logo;


            if(appearingMode.value === 'dark'){
                if(activeDesign.value.logo.dark){
                    logo = activeDesign.value.logo.dark
                }else if(authScreenConf.logo.dark) {
                    logo = authScreenConf.darkLogo
                }else{
                    logo = appConf.logo.dark
                }
            } else {
                if(activeDesign.value.logo.light){
                    logo = activeDesign.value.logo.light
                }else if(authScreenConf.logo.light) {
                    logo = authScreenConf.logo.light
                }else{
                    logo = appConf.logo.light
                }
            }

            return logo;
        });

        /*Design Template Changer*/
        const activeDesignIndex = ref(0)
        const changeBg = () => {
            if (authDesigns.length - 1 > activeDesignIndex.value) {
                activeDesignIndex.value++
            } else {
                activeDesignIndex.value = 0
            }
        };
        const activeDesign = computed(() => {
            return authDesigns[activeDesignIndex.value]
        })

        return {
            authDesigns,
            form,
            darkMode,
            appearingMode,
            changeLang,
            changeTheme,
            changeBg,
            activeDesign,
            submit,
            languages,
            locale,
            deviceType,
            appConf,
            authScreenConf,
            temporaryLogo,
            t,
            tm,
            v
        };
    },

    methods: {
        beforeStyle(event) {
            event.style.opacity = 0;
        },
        enterStyle(event) {
            event.style.opacity = 1;
            event.style.transition = `all 1s linear`;
        }
    },
    computed: {
        errors() {
            return this.$page.props.errors;
        },

        hasErrors() {
            return Object.keys(this.errors).length > 0;
        }
    }
});
</script>
