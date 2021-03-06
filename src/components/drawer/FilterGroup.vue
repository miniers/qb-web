<template>
  <v-list-group
    v-model="model"
    :prepend-icon="model ? group.icon : group['icon-alt']"
    class="filter-group"
  >
    <template v-slot:activator>
      <v-list-tile>
        <v-list-tile-content>
          <v-list-tile-title v-class:primary--text="selected !== null">
            {{ group.title }}
          </v-list-tile-title>
        </v-list-tile-content>
      </v-list-tile>
    </template>
    <v-list-tile
      v-for="(child, i) in group.children"
      :key="i"
      v-class:primary--text="selected === child.key"
      @click.stop="select(child.key)"
    >
      <v-list-tile-action>
        <v-icon v-if="isFontIcon(child.icon)">{{ child.icon }}</v-icon>
        <div v-else>
          <v-img :src="child.icon" width='22px' height="22px" />
        </div>
      </v-list-tile-action>
      <v-list-tile-content>
        <v-list-tile-title>
          <template v-if="child.append">
            <v-layout>
              {{ child.title }}
              <v-spacer />
              {{ child.append }}
            </v-layout>
          </template>
          <template v-else>
            {{ child.title }}
          </template>
        </v-list-tile-title>
      </v-list-tile-content>
      <v-list-tile-action v-if="group.action">
        <v-menu bottom left>
          <template #activator="{ on }">
            <v-btn
                    icon
                    @click.stop="on.click"
            >
              <v-icon >mdi-dots-vertical</v-icon>
            </v-btn>
          </template>

          <v-list>
            <v-list-tile
                    v-for="(item, i) in group.action"
                    :key="i"
                    v-if="item.show(child)"
                    @click="item.action(child)"
            >
              <v-list-tile-title>{{ item.title }}</v-list-tile-title>
            </v-list-tile>
          </v-list>
        </v-menu>
      </v-list-tile-action>
    </v-list-tile>
  </v-list-group>
</template>

<script lang="ts">
import Vue from 'vue'
import { mapState, mapMutations } from 'vuex';
export default Vue.extend({
  props: {
    group: Object,
  },
  data() {
    return {
      model: this.group.model,
      selected: null,
    }
  },
  created() {
    this.selected = this.$store.getters.config.filter[this.group.select];
  },
  methods: {
    ...mapMutations([
      'updateConfig',
    ]),
    select(key: any) {
      this.selected = this.selected === key ? null : key;
      this.updateConfig({
        key: 'filter',
        value: {
          [this.group.select]: this.selected
        },
      });
    },
    isFontIcon(icon: string) {
      return icon.startsWith('mdi-');
    },
  },
});
</script>

<style lang="scss" scoped>
::v-deep .v-list__group__header__prepend-icon {
  padding-left: 20px;
}
::v-deep .v-list__group__header [role=listitem] {
  margin-left: -4px;
}
.v-list__tile__action {
  padding-left: 6px;
}

.filter-group ::v-deep .v-list__group__items .v-list__tile {
  height: 2.2em;
  padding: 0 4px;
}
</style>
