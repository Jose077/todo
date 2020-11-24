<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <!-- <h1 class="todos">TODO LIST</h1> -->
    </v-col>
    <v-container>
      <!-- NEW TODOS -->
      <div class="body-todo">
        <v-alert color="blue">
          <v-form v-model="valid" ref="form" @submit.prevent>
            <v-row @click.prevent="mostrartodo()" style="color: white">
              <h2 class="todo-left white--text">Novo Todo</h2>
            </v-row>
            <!-- V-SHOW TODO ======================================  -->
            <div v-show="formTodo">
              <v-text-field
                color="black"
                label="Descrição"
                v-model="newTask"
                :rules="[
                  (v) => !!v || 'Descricao is required',
                  (v) =>
                    (v && v.length <= 200) ||
                    'Descricao must be less than 10 characters',
                ]"
              ></v-text-field>
              <!-- btn enviar todo -->
              <v-btn block @click.prevent="inserirTodo()" :disabled="!valid">
                Adicionar
              </v-btn>
              <!-- \btn enviar todo -->
            </div>
          </v-form>
          <!-- \V-SHOW TODO   -->
        </v-alert>
      </div>
      <!-- /NEW TODO -->

      <!-- LIST TODOS -->
      <div class="body-todo" style="margin-top: 2.5rem">
        <v-divider class="mt-4"></v-divider>

        <v-row class="my-1" align="center">
          <strong class="mx-4 info--text text--darken-2">
            Em progresso: {{ remainingTasks }}
          </strong>

          <v-divider vertical> </v-divider>

          <strong class="mx-4 success--text text--darken-2">
            Completa: {{ completedTasks }}
          </strong>

          <v-spacer></v-spacer>

          <v-progress-circular
            :value="progress"
            class="mr-2"
          ></v-progress-circular>
        </v-row>

        <v-divider class="mb-4"></v-divider>

        <v-card v-if="tasks.length > 0">
          <v-slide-y-transition class="py-0" group tag="v-list">
            
            <!-- <v-for> -->
            <template v-for="(task, i) in tasks">
              <v-divider v-if="i !== 0" :key="`${i}-divider`"></v-divider>
              <v-list-item :key="`${i}-${task.descricao}`">
                <v-list-item-action>
                  <v-checkbox
                    v-model="task.done"
                    :color="(task.done && 'grey') || 'primary'"
                    @change="checkState(i, $event)"
                  >
                    <template v-slot:label>
                      <div
                        :class="
                          (task.done && 'grey--descricao') ||
                          'primary--descricao'
                        "
                        class="ml-4"
                        v-text="task.descricao"
                      ></div>
                    </template>
                  </v-checkbox>
                </v-list-item-action>

                <v-spacer></v-spacer>

                <v-scroll-x-transition>
                  <v-icon v-if="task.done" color="success"> mdi-check </v-icon>
                </v-scroll-x-transition>
                <!-- btn DELETE -->
                <v-icon
                  v-if="task.done"
                  color="red"
                  class="pl-1"
                  @click="deletarTodo(i)"
                >
                  x
                </v-icon>
              </v-list-item>
            </template>
          </v-slide-y-transition>
          <v-btn
            block
            elevation="2"
            color="red"
            class="white--text"
            tile
            @click="limparTodos()"
          >
            Limpar Todos</v-btn
          >
        </v-card>
      </div>
    </v-container>
  </v-row>
</template>


<script lang="ts">
import {
  Component,
  Inject,
  Model,
  Prop,
  Provide,
  Vue,
  Watch,
} from 'nuxt-property-decorator'

@Component({
  // components: { comp },
})
export default class MyComponent extends Vue {
  //DATA=
  datajson: string = ''
  formTodo: boolean = false
  getData: any
  data: any

  //<!-- DATA

  valid: boolean = true

  //Todo

  tasks: Array<{ done: boolean; descricao: string }> = []
  newTask = ''

   get completedTasks() {
     return this.tasks.filter((task) => task.done).length
  }

   get progress() {
     return (this.completedTasks / this.tasks.length) * 100
   }
   get remainingTasks() {
     return this.tasks.length - this.completedTasks
   }
  //Mounted
  mounted() {
    this.listarTodo()
  }

  //METOHDS

  // funcao para exibir v-show
  mostrartodo() {
    this.formTodo = !this.formTodo
  }

  //INSERIR TODO 
  inserirTodo() {
    this.tasks.push({
      done: false,
      descricao: this.newTask,
    })

    this.data = this.tasks
    //Salvar localStorage
    this.datajson = JSON.stringify(this.data)

    localStorage.setItem('todo', this.datajson)
    this.listarTodo()
    //@ts-ignore
    this.$refs.form.reset()
    this.formTodo = !this.formTodo
    localStorage.setItem('todo', this.datajson)
  }

  //DELETAR  1 TODO

  deletarTodo(id: number) {
    this.tasks.splice(id, 1)
    this.data = this.tasks
    this.datajson = JSON.stringify(this.data)
    localStorage.setItem('todo', this.datajson)
  }

  //Listar todos
  listarTodo() {
    this.getData = localStorage.getItem('todo')
    if (this.getData == undefined || null) {
      alert('Nenhum Todo ')
    } else {
      this.data = JSON.parse(this.getData)
      console.log(this.data)
      this.tasks = this.data
    }
  }
  //Limpa todos os Todos existentes;
  limparTodos() {
    this.tasks = []
    this.data = this.tasks
    this.datajson = JSON.stringify(this.data)
    localStorage.setItem('todo', this.datajson)
  }
  //Verifica se o Todo esta marcado
  checkState(id: number, event: any) {
    this.tasks[id].done = event
    console.log(this.tasks[id].done)
    this.data = this.tasks
    this.datajson = JSON.stringify(this.data)
    localStorage.setItem('todo', this.datajson)
  }
  // \SCRIPT TODO
}
</script>

<style>
body {
  text-align: center;
}

.body-todo {
  margin-top: 2.5rem;
  margin: auto;
  width: 50%;
  display: block;
  color: black;
}

.todo-left {
  margin: auto;
  padding-left: 1rem;
  color: black;
}

body {
  background-color: gray;
}

</style>
