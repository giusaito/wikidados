<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title>
          Wiki Dados - Teste
        </q-toolbar-title>

        <div>por Giuliano Saito</div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      class="bg-secondary"
    >
      <q-list>
        <q-item-label
          header
          class="text-white"
        >
          Navegue
        </q-item-label>

        <router-link v-for="link in rotasLista"
          :key="link.title"
          v-bind="link" 
          class="q-item q-item-type row no-wrap q-item--clickable q-link cursor-pointer q-focusable q-hoverable text-white" :to="link.link">
          <q-item-section
            avatar
          >
            <q-icon :name="link.icon" />
          </q-item-section>

          <q-item-section>
            <q-item-label>{{link.title}}</q-item-label>
            <q-item-label class="text-grey-7" caption>
              {{link.caption}}
            </q-item-label>
          </q-item-section>
        </router-link>
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import EssentialLink from 'components/EssentialLink.vue'

const routeList = [
  {
    title: 'Usuários',
    caption: 'Visualizar a lista de usuários',
    icon: 'account_circle',
    link: '/usuarios'
  },
  {
    title: 'Login',
    caption: 'efetue seu login',
    icon: 'login',
    link: '/login'
  },
];

import { defineComponent, ref } from 'vue'

export default defineComponent({
  name: 'MainLayout',

  setup () {
    const leftDrawerOpen = ref(false)

    return {
      rotasLista: routeList,
      leftDrawerOpen,
      toggleLeftDrawer () {
        leftDrawerOpen.value = !leftDrawerOpen.value
      }
    }
  }
})
</script>
