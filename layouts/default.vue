<template>
  <v-layout>
    <v-navigation-drawer
      permanent
    >
      <v-list
        density="compact"
        nav
      >
        <v-list-item title="Characters" value="Characters" href="/">
        </v-list-item>
        <v-list-item title="Locations" value="Locations" href="/locations">
        </v-list-item>
        <v-list-item title="Episodes" value="Episodes" href="/episodes">
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-main>
      <slot />
    </v-main>
  </v-layout>
</template>
