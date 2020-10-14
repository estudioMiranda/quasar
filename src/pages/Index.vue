<template>
  <div class="q-pa-md q-gutter-sm">
    <q-editor
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
    <q-card class="row" flat bordered v-for="(item, index) in tasks" :key="index">
      <q-card-section :class="item.estado ? 'tachar' : ''" class="col" v-html="item.texto" />
      <q-btn flat color="blue" @click="item.estado = !item.estado" >Acción</q-btn>
      <q-btn flat color="red" @click="eliminar(index)">Eliminar</q-btn>
    </q-card>
  </div>
</template>
<script>
export default {
  data () {
    return {
      editor: '',
      tasks: [
        { texto: 'tarea 1', estado: false },
        { texto: 'tarea 2', estado: true },
        { texto: 'tarea 3', estado: false }
      ]
    }
  },
  methods: {
    saveWork () {
      this.tasks.push({
        texto: this.editor,
        estado: false
      })
      this.$q.notify({
        message: 'Saved your text to local storage',
        color: 'green-4',
        textColor: 'white',
        icon: 'cloud_done'
      })
    },
    eliminar (index) {
      this.$q.dialog({
        title: '¿Desea eliminar tarea?',
        message: 'No podrá recuperar la tarea',
        cancel: true,
        persistent: true
      }).onOk(() => {
        // console.log('>>>> OK')
        this.tasks.splice(index, 1)
      })
      
    }
  }
}
</script>
<style>
.tachar {
  text-decoration: line-through;
}
</style>
