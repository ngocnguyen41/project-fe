<template>
    <div>
      <div
        class="bg-[url('~/assets/img/login-bg.jpg')] h-screen overflow-hidden flex items-center justify-center"
      >
        <div class="bg-white lg:w-3/12 md:6/12 w-10/12 shadow-3xl">
          <div
            class="absolute left-1/2 transform -translate-x-1/2 -translate-y-52 rounded-full p-4 md:p-8"
          >
            <img src="~/assets/img/big-logo.png" class="h-24" alt="Logo" />
          </div>
          <a-form
            ref="formLogin"
            layout="vertical"
            :model="formState"
            :rules="rules"
            :hide-required-mark="true"
          >
            <a-form-item :label="$t('user.account')" name="username">
              <a-input
                v-model:value="formState.username"
                size="large"
                :placeholder="$t('user.account')"
                @press-enter="login"
              />
            </a-form-item>
            <a-form-item :label="$t('user.password')" name="password">
              <a-input-password
                v-model:value="formState.password"
                size="large"
                placeholder="********"
                @press-enter="login"
              />
            </a-form-item>
            <p class="text-cyan-800">{{ $t('button.forgot_password') }}</p>
            <a-form-item>
              <a-button
                class="mt-4 w-full bg-cyan-800"
                type="primary"
                size="large"
                :loading="loading"
                @click="login"
              >
                {{ $t('button.login') }}
              </a-button>
            </a-form-item>
          </a-form>
        </div>
      </div>
  
      <div class="absolute top-6 right-10">
        <a-dropdown :placement="placement" :arrow="{ pointAtCenter: true }">
          <GlobalOutlined class="text-2xl text-slate-500" />
          <template #overlay>
            <a-menu>
              <a-menu-item @click="changeLocale('vi')">
                <div class="flex">
                  <img
                    src="~/assets/img/languages/vietnamese.png"
                    class="h-6 w-10"
                    alt="Logo"
                  />
                  <span class="ml-2">Tiếng Việt</span>
                </div>
              </a-menu-item>
              <a-menu-item @click="changeLocale('en')">
                <div class="flex">
                  <img
                    src="~/assets/img/languages/english.png"
                    class="h-6 w-10"
                    alt="Logo"
                  />
                  <span class="ml-2"> English </span>
                </div>
              </a-menu-item>
            </a-menu>
          </template>
        </a-dropdown>
      </div>
    </div>
  </template>

<script setup>
import { reactive } from 'vue'
import { message } from 'ant-design-vue'
import { useI18n } from 'vue-i18n'
import { useFormSubmit } from '../composables/useFormSubmit'
import { login as loginApi } from '~/api/auths'

definePageMeta({
  layout: 'none',
})

const { t, locale } = useI18n()

const formLogin = ref()

const rules = {
  username: [
    {
      required: true,
      message: t('validate.account_required'),
      trigger: 'change',
    },
  ],
  password: [
    {
      required: true,
      message: t('validate.password_required'),
      trigger: 'change',
    },
  ],
}

const formState = reactive({
  username: '',
  password: '',
})

const login = async () => {
  try {
    const response = await submit()

    if (response) {
      const token = useCookie('token')
      token.value = response.data.access_token

      location.href = '/'
    }
  } catch (e) {
    console.log(e)
    message.error(t('message.login_failed'))
  }
}

const { submit, loading } = useFormSubmit({
  formRef: formLogin,
  apiSubmit: loginApi,
  formState,
})

const changeLocale = (lang) => {
  localStorage.setItem('locale', lang)

  locale.value = lang
}
</script>