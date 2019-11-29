<template>
  <form @reset.prevent="onReset" @submit.prevent="send">
    <slot :filter="filter" />
  </form>
</template>

<script>
import debounce from 'lodash.debounce'

export default {
  name: 'VueFilter',

  props: {
    defaults: {
      type: Object,
      required: true
    }
  },

  data () {
    return {
      filter: Object.assign({}, this.defaults)
    }
  },

  created () {
    for (let key in this.defaults) {
      if (this.$route.query[key]) {
        this.filter[key] = this.$route.query[key]
      }

      this.$watch(`filter.${key}`, debounce(this.send, 600))
    }
  },

  methods: {
    onReset () {
      for (let key in this.defaults) {
        this.filter[key] = this.defaults[key]
      }

      this.send()
    },

    send () {
      let searchParams = JSON.parse(JSON.stringify(this.filter))
      let route = Object.assign({}, this.$route)

      route.query = searchParams

      this.$router.push(route).catch(err => err)
    }
  }
}
</script>
