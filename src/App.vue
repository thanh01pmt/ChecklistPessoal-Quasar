<template>
  <div id="q-app">
    <q-layout view="lHr lpR lFr">
      <q-header
        elevated
        class="bg-primary text-white"
      >
        <q-toolbar>
          <q-btn
            dense
            flat
            round
            icon="menu"
            @click="left = !left"
          />

          <q-toolbar-title>{{ $t('app.title') }}</q-toolbar-title>
          <q-btn
            class="q-mr-xs"
            dense
            round
            flat
            icon="new_releases"
          >
            <q-badge
              color="red"
              floating
              transparent
            >
              {{ version }}
            </q-badge>
          </q-btn>
          <q-btn
            v-if="user.uid == null"
            flat
            round
            dense
            icon="vpn_key"
            class="q-mr-xs"
            to="/login"
          />
          <q-btn
            v-if="user.uid != null"
            alt="Sair"
            flat
            round
            dense
            icon="exit_to_app"
            @click="logout"
          />
        </q-toolbar>
        <q-breadcrumbs
          v-if="$q.platform.is.mobile"
          align="center"
          active-color="white"
          style="font-size: 14px"
        >
          <q-breadcrumbs-el
            icon="home"
            to="/folder"
          />
          <q-breadcrumbs-el
            v-if="currentFolder.id != null"
            :to="'/board/' + currentFolder.id"
            :label="currentFolder.title"
            icon="folder"
          />
          <q-breadcrumbs-el
            v-if="currentBoard.id != null"
            :label="currentBoard.title"
            icon="folder"
          />
        </q-breadcrumbs>

        <q-breadcrumbs
          v-if="$q.platform.is.desktop"
          align="center"
          active-color="white"
          style="font-size: 20px"
        >
          <q-breadcrumbs-el
            label="Home"
            icon="home"
            to="/folder"
          />
          <q-breadcrumbs-el
            v-if="currentFolder.id != null"
            :to="'/board/' + currentFolder.id"
            :label="currentFolder.title"
            icon="folder"
          />
          <q-breadcrumbs-el
            v-if="currentBoard.id != null"
            :label="currentBoard.title"
            icon="folder"
          />
        </q-breadcrumbs>
      </q-header>

      <q-drawer
        v-model="left"
        :width="200"
        side="left"
        overlay
        bordered
      >
        <q-scroll-area class="scrollArea">
          <q-list padding>
            <q-item
              v-ripple
              clickable
              to="/folder"
            >
              <q-item-section avatar>
                <q-icon name="dashboard" />
              </q-item-section>

              <q-item-section>{{ $t('app.menuDrawer.home') }}</q-item-section>
            </q-item>
            <q-item
              v-if="user"
              v-ripple
              clickable
              to="/changepassword"
            >
              <q-item-section avatar>
                <q-icon name="vpn_key" />
              </q-item-section>

              <q-item-section>{{ $t('app.menuDrawer.changePassword') }}</q-item-section>
            </q-item>

            <q-item
              v-ripple
              clickable
              to="/about"
            >
              <q-item-section avatar>
                <q-icon name="question_answer" />
              </q-item-section>

              <q-item-section>{{ $t('app.menuDrawer.about') }}</q-item-section>
            </q-item>
          </q-list>
        </q-scroll-area>

        <q-img
          class="absolute-top"
          src="https://cdn.quasar.dev/img/material.png"
          style="height: 150px"
        >
          <div class="absolute-bottom bg-transparent">
            <q-avatar
              size="56px"
              class="q-mb-sm"
            >
              <img src="https://cdn.quasar.dev/img/boy-avatar.png">
            </q-avatar>
            <div class="text-weight-bold">
              {{ user.username }}
            </div>
            <div>{{ user.email }}</div>
          </div>
        </q-img>
      </q-drawer>

      <q-page-container>
        <router-view />
      </q-page-container>

      <q-footer
        reveal
        elevated
        class="bg-grey-8 text-white"
      >
        <q-toolbar>
          <q-toolbar-title>{{ $t('app.footerMessage') }} - {{ version }}</q-toolbar-title>
        </q-toolbar>
      </q-footer>
    </q-layout>
  </div>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      left: false,
      langs: [
        {
          label: 'Brazilian - PT',
          value: 'pt-br'
        },
        {
          label: 'English - US',
          value: 'en-us'
        },
        {
          label: 'Spanish - ES',
          value: 'es-es'
        }
      ],
      lang: this.$i18n.locale
    }
  },
  mounted () {
    this.checkLanguage()
    this.$store.dispatch('boot')
  },
  methods: {
    async checkLanguage () {
      const localeClientLang = this.$q.lang.getLocale()
      for (const language of this.$i18n.availableLocales) {
        if (language === localeClientLang) {
          this.lang = localeClientLang
        }
      }
    },
    logout () {
      this.$firebase.auth().signOut()
      this.$q.cookies.remove('user')
      this.$store.dispatch('boot')
      this.$router.replace('login')
    }
  }
}
</script>

<style>
</style>
