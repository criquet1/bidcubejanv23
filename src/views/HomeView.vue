<template>
  <h3><i>Create a cube</i></h3>
  
  <div class="form-container">
    <form>

      <base-input
      v-model="cube.volume"
      label="Cubic volume"
      type="number"
      />

      <base-select 
        :options="cuts"
        v-model="cube.height"
        label="Select a cut"
      />
    
      <base-select 
        :options="spacings"
        v-model="cube.spacing"
        label="Select a spacing "
      />

    </form>
    <!-- <pre>{{ cube }}</pre> -->
    <h4>{{ cube.name }}</h4>
    <h5>Total Volume : {{ totalVolume }}m³</h5>
    <h5>A spacing of {{ cube.spacing }}m </h5>
    <h5>{{ numberOfHoles }} holes X {{ numberOfHoles }} holes</h5>
    <h5>A total of {{ numberOfHoles * numberOfHoles }} holes</h5>
  </div>
</template>
<script>
import BaseInput from '../components/BaseInput.vue'
import BaseSelect from '../components/BaseSelect.vue'

export default {
  components: { BaseInput, BaseSelect },
  data() {
    return {
      selected: 1.8,
      spacings: [
        1.8, 2.1, 2.4, 2.7, 2.75, 3, 3.3, 3.6, 3.9 
      ],
      cuts: [
        3, 4, 5, 6, 7, 8, 9, 10, 11
      ],
      categories: [
        '25 000 m³',
        '5 000 m³',
        '2 000 m³'
      ],
      cube: {
        id: 0,
        length: 0,
        width: 0,
        height: 0,
        name: '',
        volume: 0,
        spacing: 0,
        totalVolume: 0
      },
    }
  },
computed: {
  totalVolume() {
    return this.cube.length > 0 ? (this.cube.length * this.cube.width * this.cube.height).toFixed(1) : 0
  },
  numberOfHoles() {
    this.cube.length = (Math.sqrt(this.cube.volume/this.cube.height)).toFixed(1)
    this.cube.width = this.cube.length
    this.cube.totalVolume = (this.cube.length * this.cube.width * this.cube.height).toFixed(1)
    this.cube.name = this.cube.height + 'm X ' + this.cube.width + 'm X ' + this.cube.length + 'm'
    const holes = Math.ceil(this.cube.length / this.cube.spacing)
      return this.cube.spacing > 0 && this.cube.volume >= 0 ? holes : 0
  }
},
  methods: {
    onSubmit() {
      const cube = {
        ...this.cube,
        id: uuidv4(),
        organizer: this.userStore.user
      }
      this.cubeStore
        .createCube(cube)
        .then(() => {
          this.$router.push({
            name: 'CubeDetails',
            params: { id: cube.id }
          })
        })
        .catch(error => {
          this.$router.push({
            name: 'ErrorDisplay',
            params: { error: error }
          })
        })
    }
  }
}
</script>
