<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>Upload a dog img, get pictures of the same dog breed.</p>
    <input type="file" @change="onFileSelected" />

    <div id="preview">
    <img v-if="url" :src="url" />
  </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  name: "HelloWorld",
  props: {
    msg: String
  },


  setup() {

    var url = ref('')

    function onFileSelected(event) {
      const file = event.target.files[0]
      url.value = URL.createObjectURL(file)
    }

    return {
      onFileSelected: onFileSelected,
      url: url
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#preview {
  display: flex;
  justify-content: center;
  align-items: center;
}

#preview img {
  max-width: 100%;
  max-height: 500px;
}
</style>
