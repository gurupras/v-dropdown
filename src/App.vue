<script setup>
import { defineComponent } from 'vue'
import Dropdown from './components/Dropdown.vue'
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup
</script>

<template>
<div style="text-align: center">
  <div class="container has-text-centered">
    <div class="section">
      <h1 class="title is-1">Dropdown</h1>
      <div class="columns is-6 is-offset-3">
        <div class="column">
          <button class="button" @click="addItem">Add Item</button>
        </div>
        <div class="column">
          <div class="field">
            <label class="checkbox">
              <input type="checkbox" v-model="show">
              Show Dropdown
            </label>
          </div>
        </div>
      </div>
    </div>
    <div class="section">
      <Dropdown v-model:show="show" :transition-duration="300" max-height="500px">
      <template v-slot:dropdown>
        <div class="dropdown-trigger">
          <button class="button" @click="show = !show">Dropdown List</button>
          <span class="icon is-small"></span>
        </div>
      </template>
      <template v-slot:content>
        <a class="dropdown-item" v-for="item in items" :key="item">
          {{item}}
        </a>
      </template>
      </Dropdown>
    </div>
  </div>
</div>
</template>

<script>
let itemIdx = 0

export default defineComponent({
  data () {
    return {
      show: false,
      items: []
    }
  },
  methods: {
    addItem () {
      this.items.push(`Item - ${itemIdx++}`)
    }
  },
  mounted () {
    [...Array(5)].map(this.addItem)
  }
})
</script>

<style lang="scss">
</style>
