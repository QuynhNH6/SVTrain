<template>
  <b-modal
    hide-footer
    size="lg"
    ref="modal"
    v-on:hidden="onHidden"
    v-on:shown="onShown"
  >
    <div slot="modal-title">
      <b-form inline>
        <svg viewBox="0 0 16 16" width="1em" height="1em" focusable="false" role="img" xmlns="http://www.w3.org/2000/svg" fill="currentColor" class="bi-zoom-in mx-auto b-icon bi">
          <g data-v-41be6633="">
            <path fill-rule="evenodd" d="M6.5 12a5.5 5.5 0 1 0 0-11 5.5 5.5 0 0 0 0 11zM13 6.5a6.5 6.5 0 1 1-13 0 6.5 6.5 0 0 1 13 0z"></path>
            <path d="M10.344 11.742c.03.04.062.078.098.115l3.85 3.85a1 1 0 0 0 1.415-1.414l-3.85-3.85a1.007 1.007 0 0 0-.115-.1 6.538 6.538 0 0 1-1.398 1.4z"></path>
            <path fill-rule="evenodd" d="M6.5 3a.5.5 0 0 1 .5.5V6h2.5a.5.5 0 0 1 0 1H7v2.5a.5.5 0 0 1-1 0V7H3.5a.5.5 0 0 1 0-1H6V3.5a.5.5 0 0 1 .5-.5z"></path>
          </g>
        </svg>
        <b-form-select
          v-model="magnify"
          id="inline-form-custom-select-pref"
          class="ml-4 mb-2 mr-sm-2 mb-sm-0"
          :options="[{ text: 'Off', value: 1 }, { text: '2x', value: 2 }, { text: '4x', value: 4 }, { text: '8x', value: 8 }]"
        ></b-form-select>
      </b-form>
    </div>
    <ShowImage ref="show_image" v-if="fileType===types.image" v-bind:file="file" @zoom-change="magnify = $event"/>
    <ShowJSON v-if="fileType===types.json" v-on:saved="closeModal" v-bind:file="file"/>
    <ShowTFSettings v-if="fileType===types.tfsettings" v-on:saved="closeModal" v-bind:file="file"/>
    <div v-if="fileType===types.unsupported">Files with that type aren't supported</div>
    <div v-if="fileType===types.image" class="file-control">
      <div class="prev" @click="$emit('navigate','prev')">
        <slot name="prev">‹</slot>
      </div>
      <div class="next" @click="$emit('navigate','next')">
        <slot name="next">›</slot>
      </div>
    </div>
  </b-modal>
</template>

<script>
import ShowImage from './ShowImage.vue'
import ShowJSON from './ShowJSON.vue'
import ShowTFSettings from './ShowTFSettings.vue'

export default {
  name: 'ShowFile',
  props: {
    file: { type: Object, required: true },
  },
  components: { ShowImage, ShowJSON, ShowTFSettings },
  data() {
    return {
      modal: null,
      types: {
        image: 'image',
        json: 'json',
        tfsettings: 'tfsettings',
        unsupported: 'unsupported',
      },
      magnify: 1,
    }
  },
  methods: {
    show() {
      this.$refs.modal.show()
    },
    onShown(e) {
      console.log('show file shown')
      this.$emit('shown', e)
    },
    onHidden(e) {
      console.log('show file hidden')
      this.$emit('hidden', e)
    },
    closeModal() {
      this.$refs.modal.hide()
    },
  },
  computed: {
    fileType() {
      if (typeof this.file.path !== 'string') return 'unsupported'
      const p = this.file.path.toLowerCase()
      const jsonRegexp = /\.json$/
      const tfsettingRegexp = /tfsettings\.json$/
      const imageRegexp = /\.(jpeg|jpg|gif|bmp|png)$/
      if (imageRegexp.test(p)) {
        return this.types.image
      } if (tfsettingRegexp.test(p)) {
        return this.types.tfsettings
      } if (jsonRegexp.test(p)) {
        return this.types.json
      }
      return this.types.unsupported
    },
  },
  watch: {
    magnify() {
      this.$refs.show_image.options.zoomFactor = this.magnify
    },
  },
}
</script>

<style lang="scss" scoped>

</style>
