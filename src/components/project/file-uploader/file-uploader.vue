
<template lang="pug">
  div
    input#fileUploader(ref="fileUploader" type="file", accept="image/*", @change="detectFiles($event.target.files)")
    q-input.full-width(
      readonly
      label="Upload picture"
      @click="trigger"
      :value="fileName"
      :after="[{icon: 'mdi-library-plus'}]"
      stack-label="Select file to upload"
    )
</template>

<script>
import { mapMutations } from 'vuex'

export default {
  name: 'u-file-uploader',
  data () {
    return {
      progressUpload: 0,
      file: File,
      uploadTask: '',
      downloadURL: '',
      fileName: ''
    }
  },
  methods: {
    ...mapMutations('project', ['setProjectImageUrl']),
    detectFiles (fileList) {
      this.fileName = this.$refs.fileUploader.files.item(0).name
      Array.from(Array(fileList.length).keys()).map(x => {
        this.upload(fileList[x])
      })
    },
    trigger () {
      this.$refs.fileUploader.click()
    },
    upload () {
      const vm = this
      this.$emit('cuzao', 'asd')
      vm.uploadTask = vm.$firebaseStorage.child(`images/${Date.now()}`).put(vm.$refs.fileUploader.files.item(0))
      vm.uploadTask.then(function (snapshot) {
        vm.uploadTask.snapshot.ref.getDownloadURL().then(function (downloadURL) {
          vm.downloadURL = downloadURL
          vm.$store.commit('project/setProjectImageUrl', vm.downloadURL)
        }).catch((err) => {
          vm.$q.notify(err.message)
        })
      }).catch((err) => {
        vm.$q.notify(err.message)
      })
    }
  },
  mounted () {
  },
  watch: {
    uploadTask () {
      this.uploadTask.on('state_changed', function (sp) {
        this.progressUpload = Math.floor(sp.bytesTransferred / sp.totalBytes * 100)
      })
    }
  }
}
</script>

<style lang="stylus">
.progress-bar {
  margin 10px 0
}
input {
  display none
}
</style>
