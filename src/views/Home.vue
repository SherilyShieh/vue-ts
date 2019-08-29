<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" />
    <HelloWorld msg="Welcome to Your Vue.js + TypeScript App" />
    <Hello msg="ts 入门" @add-features="onAddFeature"/>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import HelloWorld from "@/components/HelloWorld.vue"; // @ is an alias to /src
import Hello, { Feature } from '@/components/Hello.vue';

@Component({
  components: {
    HelloWorld, Hello
  }
})
export default class Home extends Vue {
  onAddFeature(feature: Feature) {
    console.log(feature.name)
  }
}
</script>
