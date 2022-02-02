<template>
  <div class="items">
    <div class="container">
      <section>
        <form @submit.prevent="createItem">
          <input type="text" placeholder="Item" v-model="form.name">
          <input type="text" placeholder="Quantidade" v-model="form.ammount">
          <input type="text" placeholder="Unidade" v-model="form.unity">
          <button type="submit">Adicionar</button>
        </form>
      </section>
      <section>
        <h5 class="title">Lista de compras</h5>
        <ul>
          <li v-for="item in items" :key="item.id">
            <p>{{item.ammount + ' ' + item.unity.toUpperCase() + ' de ' + item.name  }}</p>
            <a class="destroy" @click="destroyItem(item.id)"></a>
          </li>
        </ul>
      </section>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import axios from '@/utils/axios'

interface Item {
  id: string,
  name: string,
  ammount: Number,
  unity: string
}

export default defineComponent({
  data() {
    return {
      items: [] as Item[],
      form: {
        name: '',
        ammount: 0,
        unity: ''
      }
    }
  },
  created() {
    this.fetchItems()
  },
  methods: {
    async fetchItems(){
      try{
        const { data } = await axios.get('/items')
        this.items = data
      } catch(error) {
        console.warn(error)
      }
    },
    async createItem() {
      const {data} = await axios.post('/items', this.form)
      this.items.push(data)
      this.form.name = ''
      this.form.unity = ''
      this.form.ammount = 0
    },
    async destroyItem(id: string){
      try{
        await axios.delete(`/items/${id}`)
  
        const itemIndex = this.items.findIndex(item => item.id = id)
        this.items.splice(itemIndex, 1)
      } catch(error) {
        console.warn(error)
      }
    }
  }
})
</script>

<style scoped>
.container {
  margin: 4rem auto;
  max-width: 500px;
  width: 90%;
  display: grid;
  grid-gap: 2.5rem;
}

.title {
  font-size: 1rem;
  font-weight: 500;
  margin: 0.7rem 0;
}

form {
  display: grid;
  grid-gap: 1rem;
}

input {
  background: transparent;
  border: 1px solid #999fc6;
  border-radius: 1rem;
  padding: 0.6rem;
  outline: none;
  color: #e1e8ef;
}

input::placeholder {
  color: #999fc6;
}

button {
  background-color: #2d6cea;
  color: #e1e8ef;
  border: none;
  border-radius: 1rem;
  padding: 0.6rem 1.5rem;
  width: max-content;
  transition: all 0.3s linear;
  outline: none;
  cursor: pointer;
  box-shadow: 0 0 5px 3px rgba(45, 108, 234, 0.3);
}

button:hover {
  background-color: #1b5cdc;
}

p {
  margin: 0;
}

ul {
  padding: 0;
  margin: 0;
  display: grid;
  grid-gap: 1rem;
}

li {
  background-color: #2b3a4e;
  padding: 1.2rem 1rem;
  border-radius: 1rem;
  position: relative;
  list-style: none;
  color: #8b98a8;
}

.destroy {
  background-color: #d53e6b;
  width: 24px;
  height: 24px;
  border-radius: 0.5rem;
  cursor: pointer;
  transition: all 0.2s linear;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  right: 1.3rem;
}

.destroy:before,
.destroy:after {
  content: '';
  width: 3px;
  height: 13px;
  background-color: #ececf6;
  border-radius: 1rem;
  position: absolute;
  top: 50%;
  left: 50%;
}

.destroy:before {
  transform: translate(-50%, -50%) rotate(45deg);
}

.destroy:after {
  transform: translate(-50%, -50%) rotate(130deg);
}

.destroy:hover {
  background-color: #984848;
}
</style>