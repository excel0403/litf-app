<template>
  <transition name="v">
    <div class="nighttime" v-if="show">
    
      <div v-if="checkIfDone" class="checkIfDone">
          <div class="alert alert-success" role="alert">
              <p>
                <b><router-link to="/Daytime" class="text-success">GO TO THE DAYTME REVEAL</router-link></b> 
              </p>
          </div>            
      </div>

      <div class="eyes">
        <div class="eye_holder" v-for="(animal, index) in animals" :key="animal.id">
          <img 
          v-bind:class="classNameByIndex(index)"
          v-bind:src="require('../assets/eyes/' + (animal.eye_state == 'open' ? animal.eyes_open : animal.eyes))"
          v-bind:alt="animal.name" 
          v-bind:title="animal.name" 
          v-on:click="showAnimal(animal)" style="cursor: pointer;"/>
        </div>
      </div>

      <div class = heart>
        <img
        src = "@/assets/images/greenhearttransparent2.png"
        height = "50"
        width = "50"
        v-on:click="showInfo()" style="cursor: pointer;"
        />
      </div>    

      <audio autoplay loop ref="forest_audio">
        <source src="@/assets/audio/forest.mp3" type="audio/ogg">
      </audio>

      <div class="bottom_nav">
        <div style="width: 100%; text-align: center;">
          <p>
            <router-link to="/">Home</router-link>
            <!--&nbsp;&nbsp;<a class="text-success" href="#" v-on:click="startOver" style="cursor: pointer;">Start Over</a>-->
          </p>
        </div>
        
        <div>
          <div v-for="(animal) in animals" :key="animal.id">
              <div v-show="animal.eye_state == 'open'" class="progress_bar_item">
                {{animal.name}}
              </div>
          </div>
        </div>      

      </div>
    </div>
  </transition>
</template>

<style scoped lang="scss">
.progress_bar_item {
  display: inline;
  float: left;
  width: 65px;
  padding: 2px;
  color: #000000;
  background-color: #FE914A;
  border-radius: 10px;
  margin: 10px;
}
.eye_lower {
  padding-top: 100px;
}
.eye {

}
.eyes { 
  top: 50%;
  position: absolute; 
  width: 100%;
  text-align: center;
  bottom: 150px; 
  margin: auto;
}
.eye_holder {
  display: inline;
  padding: 15px;
}
.nighttime {
  background-image: url('~@/assets/backgrounds/Forest.png');
  height: 100%;
  width: 100%;
  background-repeat: repeat-x;
  background-size: contain;
}
.checkIfDone {
  padding: 20px; 
  width: 400px; 
  margin: auto; 
  padding-top: 100px;  
}
.heart {
  padding-top: 350px;
  padding-left: 75%;
}
.v-enter { /* starting style */
  opacity: 0;
}
.v-enter-active { /* active entering style */
  transition: opacity 3s ease-in; 
}
.v-leave-active { /* active leaving style */
  transition: opacity 0.5s ease-out; 
}
.v-leave-to { /* ending style */
  opacity: 0;
}
.alert {
  font-family: Baskerville;
  margin-top: 200px;
}
</style>

<script>
import GreenHeartComponent from '../components/GreenHeart.vue'
import $ from 'jquery'

export default {
  name: 'Nighttime',
  components: {
  },
  data () {
    return {
      show: false,
      animals: null,
      loading: true,
      errored: false
    }
  },
  computed: {
    /*
    animalsClicked() {
        let animalList = '';
        $.each(this.animals, function(key, animal) {
            if (animal.eye_state === 'open') animalList += (animalList != '' ? ', ' : '') + animal.name;
        });
        return animalList;
    },
    */
    checkIfDone() {
        let done = true;
        $.each(this.animals, function(key, animal) {
            if (animal.eye_state === 'blink') done = false;
        });
        return done;
    }
  },
  methods:{
    startOver: function() {
        $.each(this.animals, function(key, animal) {
            animal.eye_state = 'blink';
        });
    },
    classNameByIndex: function (index) {
      return index % 2 == (0 || 1) ? 'eye' : 'eye_lower';
    },
    showAnimal(animal) {
      animal.eye_state = 'open';
      console.log('show animal: ' + animal.name);
      this.$parent.showModal(animal);
    },
    getAnimals () {
      this.animals = this.$store.getters.getAnimalList;
    },
    showInfo () {
        this.$modal.show(
          GreenHeartComponent,
          {},
          {draggable: true}
        )
    }
  },
  mounted() {
    this.show = true;
      if (this.$refs.forest_audio != null) {
        this.$refs.forest_audio.load();
        this.$refs.forest_audio.volume = 0.20;
        this.$refs.forest_audio.play();
      }
  },
  beforeMount(){
    this.getAnimals()
  }  
}
</script>