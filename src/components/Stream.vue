<template>
  <QrcodeStream
    :camera="camera"
    @decode="handleDecode"
    @init="handleInit"
  >
    <div
      v-show="cameraForzen"
      class="validation-layer"
    >
      <div
        v-if="validating"
        class="validation-notice"
      >
        Long validation in progress...
      </div>

      <div
        v-else
        class="validation-result"
      >
        <div
          v-if="!isValid"
          class="is-invalid"
        >
          This is NOT a URL
        </div>
      </div>
    </div>
  </QrcodeStream>
</template>

<script>
import { QrcodeStream } from 'vue-qrcode-reader'
export default {
  name: 'Stream',
  components: {
    QrcodeStream
  },
  data: () => ({
    isValid: false,
    validating: false,
    camera: {},
    result: null,

    loading: false,
    firstLoad: true
  }),
  computed: {
    cameraForzen () {
      return this.camera === false || (this.loading && !this.firstLoad)
    }
  },
  methods: {
    async handleInit (promise) {
      this.loading = true

      try {
        await promise
      } catch (error) {
        console.error(error)
        this.$emit('support', false)
      } finally {
        this.firstLoad = false
        this.loading = false
      }
    },

    async handleDecode (content) {
      this.result = content

      this.stopCamera()

      this.validating = true
      this.isValid = await this.validate(content)
      this.validating = false

      if (this.isValid) {
        this.$emit('success', this.result)
        this.startCamera()
      } else {
        setTimeout(() => {
          this.startCamera()
        }, 2000)
      }
    },

    stopCamera () {
      this.camera = false
    },

    startCamera () {
      this.camera = null
    },

    validate (content) {
      return new Promise(resolve => resolve(new RegExp('^(http|https)://').test(content)))
    }
  }
}
</script>

<style lang="scss">
.qrcode-stream {
  width: 100vw;
  height: 100vh;

  &__inner-wrapper,
  &__camera,
  &__pause-frame {
    width: 100%;
    height: 100%;
  }
  &__camera,
  &__pause-frame {
    object-fit: cover;
  }
}

.validation-layer {
  position: absolute;
  width: 100%;
  height: 100%;

  background-color: rgba(255, 255, 255, .8);
  text-align: center;
  padding: 10px;

  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
}

.validation-notice,
.validation-result {
  font-size: 24px;
  font-weight: 700;
}

.is-valid {
  color: green;
}
.is-invalid {
  color: red;
}
</style>
