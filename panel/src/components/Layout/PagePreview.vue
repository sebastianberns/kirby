<template>
  <iframe
    ref="iframe"
    :src="page.previewUrl"
    class="k-page-preview"
    name="k-page-preview"
    @load="onLoaded"
  />
</template>

<script>
export default {
  props: {
    page: Object,
  },
  data() {
    return {
      scroll: 0,
    }
  },
  computed: {
    changes() {
      return this.$store.getters["form/values"](this.id);
    },
    id() {
      return this.$store.state.form.current;
    }
  },
  watch: {
    changes() {
      this.load();
    }
  },
  created() {
    this.$events.$on("model.update", this.load);
  },
  destroyed() {
    this.$events.$off("model.update", this.load);
  },
  methods: {
    load() {
      if (this.page.previewUrl) {
        this.scroll = this.$refs.iframe.contentWindow.pageYOffset;
        let form = document.createElement("form");
        form.action = this.page.previewUrl;
        form.target = "k-page-preview";
        let input = document.createElement("input");
        input.type = "hidden";
        input.name = "preview";
        input.value = JSON.stringify(this.changes);
        form.appendChild(input);
        document.body.appendChild(form);
        form.submit();
      }
    },
    onLoaded() {
      this.$refs.iframe.contentWindow.scrollTo({top: this.scroll});
    }
  }
};
</script>

<style lang="scss">
.k-page-preview {
  width: 50%;
  background: #fff;
}
</style>
