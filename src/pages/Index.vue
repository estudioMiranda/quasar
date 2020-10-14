<template>
  <div class="q-pa-md q-gutter-sm">
    <q-editor v-if="!modoEdicion"
      v-model="editor"
      :definitions="{
        save: {
          tip: 'Guarda la tarea',
          icon: 'save',
          label: 'Guardar',
          handler: saveWork
        }
      }"
      :toolbar="[
        ['bold', 'italic', 'strike', 'underline'],
       ['upload', 'save']
      ]"
    />
    <q-editor v-else
      v-model="editor"
      :definitions="{
        save: {
          tip: 'Actualizar',
          icon: 'save',
          label: 'Actulizar',
          handler: updateWork
        }
      }"
      :toolbar="[
        ['bold', 'italic', 'strike', 'underline'],
       ['upload', 'save']
      ]"
    />
    <q-card class="row" flat bordered v-for="(item, index) in tasks" :key="index">
      <q-card-section :class="item.estado ? 'tachar' : ''" class="col" v-html="item.texto" />
      <q-btn flat color="yellow" @click="editar(index, item.id)">Editar</q-btn>
      <!-- <q-btn flat color="blue" @click="item.estado = !item.estado" >Acción</q-btn> -->
      <q-btn flat color="red" @click="eliminar(index, item.id)">Eliminar</q-btn>
    </q-card>
  </div>
</template>

<script>
import { db } from "boot/firebase";
export default {
  data () {
    return {
      editor: '',
      tasks: [],
      index: null,
      modoEdicion: false,
      id: null 
    }
  },
  created(){
    this.listarTareas();
  },
  methods: {
    async listarTareas() {
      try {
        const resDB = await db.collection('tareas').get()
        resDB.forEach(res => {
          console.log(res.id);
          const tarea ={
            id: res.id,
            texto: res.data().texto, 
            estado: res.data().estado
          }
          this.tasks.push(tarea)
        })
      } catch (error) {
        console.log(error);
      }
    },
    async saveWork () {
      try {
         const resDB = await db.collection('tareas').add({
           texto: this.editor,
           estado: false
         })
         this.tasks.push({
           texto: this.editor,
           estado: false,
           id: resDB.id
      })
      this.editor= ''
      this.$q.notify({
        message: 'Saved your text to local storage',
        color: 'green-4',
        textColor: 'white',
        icon: 'cloud_done'
      }) 
      } catch (error) {
        console.log(error);
      }
  },
    eliminar (index, id) {
      this.$q.dialog({
        title: '¿Desea eliminar tarea?',
        message: 'No podrá recuperar la tarea',
        cancel: true,
        persistent: true
      }).onOk(async () => {
        // console.log('>>>> OK')
        try {
          await  db.collection('tareas').doc(id).delete()
          this.tasks.splice(index, 1)
        } catch (error) {
            console.log(error);
        }
       
      })
      
    },
    async updateWork(){
      console.log('actualizando')
      try {
        const resDB = await db.collection('tareas').doc(this.id).update({
          texto: this.editor
        })
        this.tasks[this.index].texto = this.editor
        this.$q.notify({
        message: 'Saved your text to local storage',
        color: 'green-4',
        textColor: 'white',
        icon: 'cloud_done'
      }) 
      } catch (error) {
        consola.log(error);
      }finally {
        this.modoEdicion = falso;
        this.index = null;
        this.id = null;
        this.editor = ''
      }
    },
    editar(index, id){
      this.modoEdicion = true;
      this.index = index;
      this.id = id;
      this.editor = this.tasks[index].texto
    },
  }
}
</script>
<style>
.tachar {
  text-decoration: line-through;
}
</style>
