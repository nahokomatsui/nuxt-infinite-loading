<template>
  <div class="root">
    <div class="items">
      <div v-for="item in state.items" :key="item.id" class="item">
        <div class="id">#{{ item.id }}</div>
        <div class="label">{{ item.label }}</div>
      </div>
      <infinite-loading @infinite="infiniteHandler">
        <template slot="no-more">これ以上データはありません</template>
      </infinite-loading>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive } from '@nuxtjs/composition-api'
import InfiniteLoading, { StateChanger } from 'vue-infinite-loading'
import { range } from '~/helpers/range'
import { wait } from '~/helpers/wait'

export default defineComponent({
  components: {
    InfiniteLoading,
  },
  setup() {
    const state = reactive<{ items: { id: number; label: string }[] }>({
      items: [],
    })

    const infiniteHandler = async ($state: StateChanger) => {
      await wait(2000)
      const lastId = state.items.length
        ? state.items[state.items.length - 1].id
        : 0
      const items = range(lastId + 1, lastId + 6).map((id) => ({
        id,
        label: id % 2 === 0 ? 'fugafuga' : 'hogehoge',
      }))
      state.items = state.items.concat(items)
      if (state.items.length > 20) {
        $state.complete()
      } else {
        $state.loaded()
      }
    }

    return {
      state,
      infiniteHandler,
    }
  },
})
</script>

<style scoped>
.root {
  max-width: 500px;
  margin: 1rem auto;
}

.item {
  display: flex;
  padding: 1rem;
}

.item + .item {
  border-top: 1px solid #eee;
}

.item .label {
  margin-left: 1rem;
}
</style>
