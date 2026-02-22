<template>
  <v-app id="inspire" :theme="theme.current">


    <!-- <v-navigation-drawer v-model="drawer" :temporary="!display.mdAndUp.value">
      <v-list>
        <v-list-item>

          <v-img src="/logo.png" width="100" class="mx-auto"></v-img>
          <h3 class="font-weight-bold text-center">SNC Tabulation</h3>
          <div class="text-center mt-3">
            Welcome,
            <span class="text-body-1 font-weight-bold text-green">
              {{ authStore.user?.username }}
            </span>
            <span>!</span>
          </div>
        </v-list-item>

        <v-divider></v-divider>
        <v-list-item to="/" active-class="selected-tab">
          <v-list-item-title>
            <v-icon icon="mdi-home" class="mr-2"></v-icon>
            Home
          </v-list-item-title>
        </v-list-item>
        <v-list-item to="/judge/dashboard" active-class="selected-tab">
          <v-list-item-title>
            <v-icon icon="mdi-view-dashboard" class="mr-2"></v-icon>
            Dashboard
          </v-list-item-title>
        </v-list-item>
        <v-list-item to="/judge/events" active-class="selected-tab">
          <v-list-item-title>
            <v-icon icon="mdi-calendar" class="mr-2"></v-icon>
            Events
          </v-list-item-title>
        </v-list-item>
      </v-list>
      <template #append>
        <v-btn block :rounded="false" class="bg-red" @click="authStore.logout()">
          Logout
        </v-btn>
      </template>
</v-navigation-drawer> -->

    <v-navigation-drawer theme="dark" v-model="drawer" elevation="0" :rail="rail" rail-width="120" id="main-sidebar">
      <div class="image-container">
        <v-img src="/logo.png" alt="Centered Image" class="rounded-image"></v-img>
      </div>

      <v-divider></v-divider>
      <v-list nav>
        <v-list-item-group>
          <!-- <v-list-item v-for="(item, i) in items" :key="i" active-class="border" class="logout-border" :to="item.route">
            <v-list-item-content>
              <v-icon>{{ item.icon  }}</v-icon>
              <v-list-item-subtitle>{{ item.title }}</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item> -->
          <v-list-item active-class="border" class="logout-border" to="/">
            <v-list-item-content>
              <v-icon>mdi-home</v-icon>
              <v-list-item-subtitle>Home</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
          <v-list-item active-class="border" class="logout-border" to="/judge/dashboard"
            :active="$route.path.startsWith('/judge/dashboard')">
            <v-list-item-content>
              <v-icon>mdi-view-dashboard</v-icon>
              <v-list-item-subtitle>Dashboard</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
          <v-list-item active-class="border" class="logout-border" to="/judge/events"
            :active="$route.path.startsWith('/judge/events')">
            <v-list-item-content>
              <v-icon>mdi-calendar</v-icon>
              <v-list-item-subtitle>Events</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>

        </v-list-item-group>
      </v-list>

      <template v-slot:append>
        <v-list density="compact" nav>
          <v-list-item-group>
            <v-list-item class="logout-border" @click="authStore.logout()">
              <v-list-item-content>
                <v-icon>mdi-logout</v-icon>
                <v-list-item-subtitle> Logout </v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </v-list-item-group>
        </v-list>
      </template>
    </v-navigation-drawer>

    <v-app-bar color="green" scroll-behavior="elevate">
      <v-app-bar-nav-icon @click="drawer = !drawer" />
      <v-app-bar-title class="font-weight-bold">
      
          <div class="d-flex align-center ga-2">
            <!-- <v-img src="/logo.png" max-width="80px"></v-img> -->

            <p class="font-weight-bold text-2xl">SNC Tabulation</p>
          </div>
      
      </v-app-bar-title>
      <div class="text-center mt-3">
        Welcome,
        <span class="text-body-1 font-weight-bold">
          {{ authStore.user?.username }}
        </span>
        <span>!</span>
      </div>
      <template #append>
        <div class="px-4">
          <v-icon :icon="theme.current == 'light' ? 'mdi-weather-night' : 'mdi-white-balance-sunny'"
            @click="theme.toggle()" />
        </div>
      </template>
    </v-app-bar>

    <v-main :class="theme.current === 'light' ? 'bg-grey-lighten-4' : ''">
      <Snackbar />
      <v-container fluid>
        <div class="d-flex align-center mb-4">
          <v-btn icon="mdi-arrow-left" variant="text" @click="router.back()"></v-btn>
          <v-breadcrumbs :items="breadcrumbs" class="pa-0 d-flex flex-wrap"></v-breadcrumbs>
        </div>
        <slot />
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
const display = useDisplay()
const drawer = ref(display.mdAndUp.value)
const authStore = useAuthStore()
const theme = useThemeStore()
const route = useRoute()
const router = useRouter()

const rail = ref(true);

const breadcrumbs = computed(() => {
  const crumbs = [{ title: 'Dashboard', to: '/judge/dashboard', disabled: false }]
  const pathArray = route.path.split('/').filter((i) => i)

  // Start from index 1 to skip '/judge'
  let path = '/judge'
  for (let i = 1; i < pathArray.length; i++) {
    const segment = pathArray[i]
    if (!segment) return
    path += `/${segment}`
    crumbs.push({
      title: segment?.charAt(0).toUpperCase() + segment?.slice(1),
      to: path,
      disabled: i === pathArray.length - 1,
    })
  }

  // Handle base path /judge/dashboard
  if (crumbs.length === 1 && route.path.includes('dashboard')) {
    if (crumbs[0]) crumbs[0].disabled = true
  }

  return crumbs
})
</script>

<style scoped lang="scss">
.glassmorphism-light {
  background: rgba(255, 255, 255, 0.4) !important;
  box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
}

.glassmorphism-dark {
  background: rgba(255, 255, 255, 0.1) !important;
  box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
}

a:link,
a:visited,
a:hover,
a:active {
  text-decoration: none;
  color: inherit;
}

.selected-tab .v-list-item-title,
.selected-tab .v-icon {
  color: #4caf50 !important;
  /* Green color */
}

.v-application--is-ltr .v-list--dense.v-list--nav .v-list-group--no-action>.v-list-group__items>.v-list-item {
  padding: 0 0 9px 8px;
  color: #fff;
}

#main-sidebar {
  box-shadow: 1px 0 20px rgba(0, 0, 0, 0.08);
  text-align: center;
  -webkit-box-shadow: 1px 0 20px rgba(0, 0, 0, 0.08);

  .v-navigation-drawer__border {
    display: none;
  }
}

.border {
  margin: 5px 8px 5px 8px;
  border-radius: 10px;
  border-bottom: 2px solid #4caf50 !important;
  text-decoration: none;

}

.logout-border {
  margin: 5px 8px 5px 8px;
  border-radius: 10px;
  text-decoration: none;
}

.image-container {
  width: 80%;
  margin: 15px auto;
}

.rounded-image {
  border-radius: 50%;
}
</style>
