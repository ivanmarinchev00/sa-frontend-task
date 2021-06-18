<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>Upload a dog img, get pictures of the same dog breed.</p>
    <input type="file" @change="onFileSelected" />

    <div id="preview">
      <img id="image" ref="image" v-if="url" :src="url" />
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import ml5 from "ml5";
import axios from "axios";

export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },

  setup() {
    var url = ref("");
    var imageClassifier = {};

    const image = ref(null);

    console.log(image.value + "IMAGE VALUEEE");

    function onFileSelected(event) {
      const file = event.target.files[0];
      url.value = URL.createObjectURL(file);
      loadModel();
    }

    function loadModel() {
      imageClassifier = ml5.imageClassifier("MobileNet", modelLoaded);
    }

    function modelLoaded() {
      console.log("model loaded");
      classifyImage();
    }

    function classifyImage() {
      console.log(image.value + "IMAGE HEREE");
      imageClassifier.classify(image.value, (err, results) => {
        if (err) console.log(err);
        else {
          let closestGuesses = {}
          console.log(results)
          let highesConfidence = Math.max(results[0].confidence, results[1].confidence, results[2].confidence)
          console.log(highesConfidence + "highest confidence")
          results.forEach(result => {
            if(result.confidence === highesConfidence) closestGuesses = result
          })
          // let closestGuesses = results[results.length - 1];
          let firstGuess = closestGuesses.label.split(",")[0];
          let properForm = firstGuess.replace(/\s/g, '').toLowerCase()
          getSimiliarDogImages(properForm);
        }
      });
    }

    function getSimiliarDogImages(guess) {
      axios.get(`https://dog.ceo/api/breed/${guess}/images`)
      .then(response => {
        console.log(response.data)
      })
      .catch(err => {
        console.log(err)
      })
    }

    return {
      onFileSelected: onFileSelected,
      url: url,
      image: image,
      imageClassifier: imageClassifier,
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
