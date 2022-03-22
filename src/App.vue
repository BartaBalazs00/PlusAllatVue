<template>
  <div id="app">
    <table class="table table-dark">
      <thead>
        <tr>
          <th>ID</th>
          <th>Név</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="plussallat in plussallatok" v-bind:key="plussallat.id">
          <td>{{plussallat.id}}</td>
          <td>{{plussallat.nev}}</td>
          <td>
            <button @click="deletePlussallat(plussallat.id)">Törlés</button>
            <button @click="editPlussallat(plussallat.id)">Szerkesztés</button>
          </td>
        </tr>
        <tr>
          <td>
            <input type="hidden" v-model="plussallat.id">
          </td>
          <td>
            <input type="text" v-model="plussallat.nev">
          </td>
          <td>
            <button v-if="mod_new" @click="newPlussallat" :disabled="saving">Létrehoz</button>
            <button v-if="!mod_new" @click="savePlussallat" :disabled="saving">Mentés</button>
            <button v-if="!mod_new" @click="cancelEdit" :disabled="saving">Mégse</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script>
export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      mod_new: true, 
      saving: false,
      plussallat: {
        id: null,
        nev: ''
      },
      plussallatok: []
    }
  },
   methods: {
    async loadData () {
        let Response = await fetch('http://127.0.0.1:8000/api/plussallatok')
        let data = await Response.json()
        this.plussallatok = data
        },
    async deletePlussallat(id) {
      let Response = await fetch(`http://127.0.0.1:8000/api/plussallatok/${id}`, {
        method: 'DELETE'
      })
      console.log(Response)
      await this.loadData()
    },
    async newPlussallat() {
      this.saving='disabled'
     await fetch('http://127.0.0.1:8000/api/plussallatok', {
       method: 'POST',
       headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
       },
       body: JSON.stringify(this.plussallat) 
     })
     await this.loadData()
     this.saving=false
     this.resetForm()
    },
    async savePlussallat() {
      this.saving='disabled'
     await fetch(`http://127.0.0.1:8000/api/plussallatok/${this.plussallat.id}`, {
       method: 'PATCH',
       headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
       },
       body: JSON.stringify(this.plussallat) 
     })
     await this.loadData()
     this.saving=false
     this.resetForm()
    },
    async editPlussallat(id) {
      let Response = await fetch(`http://127.0.0.1:8000/api/plussallatok/${id}`)
      let data = await Response.json()
      this.plussallat = {...data};
      this.mod_new = false
    },
    cancelEdit () {
      this.resetForm()
    },
    resetForm() {
      this.plussallat = {
        id: null,
        nev: '',
      }
      this.mod_new = true
    }
  },
  mounted() {
    this.loadData()
  }
}
</script>
<style>

</style>
