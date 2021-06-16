<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>Upload a dog img, get pictures of the same dog breed.</p>
    <input type="file" @change="onFileSelected" />

    <div id="preview">
      <img id="image" v-if="url" :src="url" />
    </div>
  </div>
</template>

<script>
import { ref } from "vue";

export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },

  setup() {
    var url = ref("");

    function onFileSelected(event) {
      const file = event.target.files[0];
      url.value = URL.createObjectURL(file);
      clasifyImage();
    }

    function clasifyImage() {
      const mobilenet = require("@tensorflow-models/mobilenet");
      const image = document.getElementById("image");

      mobilenet.load().then(model => {
        // Classify the image.
        model.classify(image).then((predictions) => {
          console.log("Predictions: ");
          console.log(predictions);
        });
      });

      // const model = new Promise((resolve) => {
      //   resolve(mobilenet.load());
      // })
      //   .then((response) => {
      //     console.log(mobilenet + "MOBILENET");
      //     return response.classify(image);
      //   })
      //   .then((result) => {
      //     console.log(result + "Result HERE");
      //     console.log(model);
      //   });
    }

    // function setUpExternalScripts() {
    //   let externalScript = document.createElement("script");
    //   externalScript.setAttribute(
    //     "src",
    //     "https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@1.0.1"
    //   );
    //   document.head.appendChild(externalScript);

    //   let externalScript2 = document.createElement("script");
    //   externalScript2.setAttribute(
    //     "src",
    //     "https://cdn.jsdelivr.net/npm/@tensorflow-models/mobilenet@1.0.0"
    //   );
    //   document.head.appendChild(externalScript2);
    // }

    // onMounted(setUpExternalScripts);

    return {
      onFileSelected: onFileSelected,
      url: url,
    };
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
  margin-top: 2%;
}
</style>
