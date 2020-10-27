<template>
  <div id="app">
    <h1>Demo</h1>
    <h2>Simple use</h2>
    <ypButton>Simple button</ypButton>
    <ypButton class="ahtung">Alternative button</ypButton>
    <ypButton class="link" inverted="1">Like a link button</ypButton>

    <h2>Nested buttons</h2>
    <p>Using classes: ahtung, link, white</p>
    <yp-button class="ahtung">Container button
      <yp-button>Other container button
        <yp-button class="link"><a href="javascript:var a" style="color:#cedaf6">&gt; inner link-button &lt;</a></yp-button>
      </yp-button>
      <div></div>
        <yp-button class="white" inverted="1">Button with "white" class must have inverted property</yp-button>


    </yp-button>

    <h2>Custom style</h2>
    <yp-button class="customGreen">Green button</yp-button>

    <h2>Radio buttons based on disabled prop</h2>
    <div>
      <yp-button v-for="(item, index) in items" @click="radioChecked(index)" :disabled="item.disabled" :key="index">Item {{index+1}}</yp-button>
    </div>

  </div>
</template>

<script>
import ypButton from './yp-button.vue'

export default {
  name: 'App',
  components: {
    ypButton
  },
  methods: {
    radioChecked  (index) {
      this.items.forEach((item, i) => {
        item.disabled = i == index
      })
    }
  },
  data: function () {
    return {
      items: [
        {disabled: true},
        {disabled: false},
        {disabled: false}
      ]
    }
  }
}
</script>

<style lang="less">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.customGreen .yp-button_button {
  background-color: green;
  border-radius: 0;
  border: 5px solid #91DCFF;
  padding: 30px;
  font-size:2rem;
  box-shadow:5px 10px 15px rgba(0,0,0,0.2);
}
.customGreen .yp-button_button:hover {
  background-color: lighten(green, 5%);
}
.customGreen .yp-button_button:active {
  color: #ccc;
  box-shadow: none;
  border: 5px solid #83BFD9;
}

</style>
