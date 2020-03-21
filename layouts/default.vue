<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-group
          v-for="item in items"
          :key="item.title"
          :to="item.to"
          v-model="item.active"
          :prepend-icon="item.icon"
        >
          <template v-slot:activator>
            <v-list-item-content>
              <v-list-item-title v-text="item.title"></v-list-item-title>
            </v-list-item-content>
          </template>

          <v-list-item
            v-for="subItem in item.items"
            :key="subItem.title"
            :to="subItem.to"
            @click=""
          >

            <v-list-item-action>
            <!-- <v-icon>{{ subItem.icon }}</v-icon> -->
          </v-list-item-action>

            <v-list-item-content>
              <v-list-item-title v-text="subItem.title"></v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list-group>
      </v-list>

      <!-- <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list> -->
    </v-navigation-drawer>
    <v-app-bar :clipped-left="clipped" fixed app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-btn icon @click.stop="miniVariant = !miniVariant">
        <v-icon>mdi-{{ `chevron-${miniVariant ? 'right' : 'left'}` }}</v-icon>
      </v-btn>
      <v-btn icon @click.stop="clipped = !clipped">
        <v-icon>mdi-application</v-icon>
      </v-btn>
      <v-btn icon @click.stop="fixed = !fixed">
        <v-icon>mdi-minus</v-icon>
      </v-btn>
      <v-toolbar-title v-text="title" />
      <v-spacer />
      <!-- <v-btn icon @click.stop="rightDrawer = !rightDrawer">
        <v-icon>mdi-menu</v-icon>
      </v-btn> -->
    </v-app-bar>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <!-- <v-navigation-drawer v-model="rightDrawer" :right="right" temporary fixed>
      <v-list>
        <v-list-item @click.native="right = !right">
          <v-list-item-action>
            <v-icon light>
              mdi-repeat
            </v-icon>
          </v-list-item-action>
          <v-list-item-title>Switch drawer (click me)</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer> -->
    <!-- <v-footer :fixed="fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer> -->
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      clipped: false,
      drawer: true,
      fixed: false,
      items: [
      // {
      //       title: 'Attractions',
      //       items: [
      //         { title: 'List Item' },
      //       ],
      //     },
      //     {
      //       title: 'Dining',
      //       active: true,
      //       items: [
      //         { title: 'Breakfast & brunch' },
      //         { title: 'New American' },
      //         { title: 'Sushi' },
      //       ],
      //     },
      //     {
      //       title: 'Education',
      //       items: [
      //         { title: 'List Item' },
      //       ],
      //     },
      //     {
      //       title: 'Family',
      //       items: [
      //         { title: 'List Item' },
      //       ],
      //     },
      //     {
      //       title: 'Health',
      //       items: [
      //         { title: 'List Item' },
      //       ],
      //     },
      //     {
      //       title: 'Office',
      //       items: [
      //         { title: 'List Item' },
      //       ],
      //     },
      //     {
      //       title: 'Promotions',
      //       items: [
      //         { title: 'List Item' },
      //       ],
      //     },
        {
          icon: 'mdi-earth',
          title: 'World',
          items: [
            {
              icon: 'mdi-arrow-right',
              title: 'Confirmed Cases',
              to: '/'
            }
          ]
        },
        {
          icon: 'mdi-map-marker-radius-outline',
          title: 'Philippines',
          items: [
            {
              icon: 'mdi-arrow-right',
              title: 'Confirmed Cases',
              to: '/ph-confirmed'
            },
            {
              icon: 'mdi-arrow-right',
              title: 'Suspected Cases'
            },
            {
              icon: 'mdi-arrow-right',
              title: 'PUIs'
            },
            {
              icon: 'mdi-arrow-right',
              title: 'Checkpoints',
              to: '/ph-checkpoints'
            },
          ]
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'COVID-19 Tracker'
    }
  }
};
</script>

<style scoped>
  .v-application .primary--text {
  color: #fff !important;
  caret-color: #fff !important;
}
</style>
