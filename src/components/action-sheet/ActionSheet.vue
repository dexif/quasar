<template>
  <q-modal
    ref="dialog"
    position="bottom"
    :content-css="contentCss"
    @close="__dismiss()"
  >

    <!-- iOS -->
    <div v-if="$q.theme === 'ios'">
      <div class="q-action-sheet">
        <div v-if="title" class="modal-header" v-html="title"></div>

        <div class="modal-scroll">
          <div v-if="gallery" class="q-action-sheet-gallery row wrap flex-center">
            <div
              v-for="(button, index) in actions"
              :key="index"
              class="cursor-pointer relative-position column inline flex-center"
              @click="close(button.handler)"
              @keydown.enter="close(button.handler)"
              :class="button.classes"
              tabindex="0"
              v-ripple.mat
            >
              <q-icon v-if="button.icon" :name="button.icon"></q-icon>
              <img v-if="button.avatar" :src="button.avatar" class="avatar">

              <span>{{ button.label }}</span>
            </div>
          </div>
          <q-list link v-else class="no-border">
            <q-item
              v-for="(button, index) in actions"
              :key="index"
              @click="close(button.handler)"
              @keydown.enter="close(button.handler)"
              tabindex="0"
              v-ripple.mat
            >
              <q-item-side :icon="button.icon" :avatar="button.avatar"></q-item-side>
              <q-item-main inset :label="button.label"></q-item-main>
            </q-item>
          </q-list>
        </div>
      </div>

      <div v-if="dismiss" class="q-action-sheet">
        <q-item
          link
          @click="close()"
          @keydown.enter="close()"
          tabindex="0"
          v-ripple.mat
        >
          <q-item-main>
            <q-item-tile label class="text-center">
              {{ dismiss.label }}
            </q-item-tile>
          </q-item-main>
        </q-item>
      </div>
    </div>

    <!-- Material theme -->
    <div v-else>
      <div v-if="title" class="modal-header" v-html="title"></div>

      <div class="modal-scroll">
        <div v-if="gallery" class="q-action-sheet-gallery row wrap flex-center">
          <div
            v-for="(button, index) in actions"
            :key="index"
            class="cursor-pointer relative-position column inline flex-center"
            @click="close(button.handler)"
            @keydown.enter="close(button.handler)"
            :class="button.classes"
            tabindex="0"
            v-ripple.mat
          >
            <q-icon v-if="button.icon" :name="button.icon"></q-icon>
            <img v-if="button.avatar" :src="button.avatar" class="avatar">

            <span>{{ button.label }}</span>
          </div>
        </div>
        <q-list link v-else class="no-border">
          <q-item
            v-for="(button, index) in actions"
            :key="index"
            @click="close(button.handler)"
            @keydown.enter="close(button.handler)"
            :class="button.classes"
            tabindex="0"
            v-ripple.mat
          >
            <q-item-side :icon="button.icon" :avatar="button.avatar"></q-item-side>
            <q-item-main inset :label="button.label"></q-item-main>
          </q-item>
        </q-list>
      </div>
    </div>
  </q-modal>
</template>

<script>
import { QModal } from '../modal'
import { QIcon } from '../icon'
import { QList, QItem, QItemSide, QItemMain, QItemTile } from '../list'
import Ripple from '../../directives/ripple'

export default {
  name: 'q-action-sheet',
  components: {
    QModal,
    QIcon,
    QList,
    QItem,
    QItemSide,
    QItemMain,
    QItemTile
  },
  directives: {
    Ripple
  },
  props: {
    title: String,
    gallery: Boolean,
    actions: {
      type: Array,
      required: true
    },
    dismiss: Object
  },
  computed: {
    contentCss () {
      if (this.$q.theme === 'ios') {
        return {backgroundColor: 'transparent'}
      }
    }
  },
  methods: {
    close (fn) {
      if (!this.$refs.dialog.active) {
        return
      }
      const hasFn = typeof fn === 'function'

      if (hasFn) {
        this.__runCancelHandler = false
      }
      this.$refs.dialog.close(() => {
        if (hasFn) {
          fn()
        }
      })
    },
    __dismiss () {
      this.$root.$destroy()
      if (this.__runCancelHandler && this.dismiss && typeof this.dismiss.handler === 'function') {
        this.dismiss.handler()
      }
    }
  },
  mounted () {
    this.__runCancelHandler = true
    this.$nextTick(() => {
      this.$refs.dialog.open()
      this.$root.quasarClose = this.close
    })
  }
}
</script>
