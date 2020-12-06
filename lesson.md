# Разбор задачи на собеседование по VueJS (создание SPA)

[video](https://www.youtube.com/watch?v=Ez5_CITkg24)

if something goes wrong during installation, can try [this](https://stackoverflow.com/questions/47675478/npm-install-errorunexpected-end-of-json-input-while-parsing-near-nt-webpack):

```bash
npm cache clean --force
```



go to [vue cli installation tutorial page](https://cli.vuejs.org/guide/installation.html)

```bash
sudo npm install -g @vue/cli
vue --version
vue create vk-vue
```

choose:

- Babel
- PWA
- Router
- Vuex
- CSS pre-processors

Use history mode

then select **Sass/SCSS (with node-sass)**, **in dedicated config files**

and os on...

OK, then...

````bash
cd vk-vue
npm run serve
````

## clean project

```bash
rm src/assets/logo.png src/components/HelloWorld.vue src/views/About.vue
```

change `src/views/Home.vue`

```vue
<template>
  <div>
  
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: 'Home',
  components: {
  }
}
</script>

```

[time 2:40](https://www.youtube.com/watch?v=Ez5_CITkg24&t=280s)

`src/App.vue`

```vue
<template>
  <div id="app">
    <div class="container">

      <router-view/>
    </div>
  </div>
</template>

<style lang="scss">
  @import '~materialize-css/dist/css/materialize.min.css';
</style>
```

`src/router/index.js`

```js
const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  }
]
```

## Add materialize css

```bash
npm install materialize-css@next
```

update `App.vue`

```vue
<style lang="scss">
  @import '~materialize-css/dist/css/materialize.min.css';
</style>
```

check styles:

`Home.vue`

```vue
  <div>
    <h1>Create</h1>
  </div>
</template>
```

in `src/main.js` import 

```js
import 'materialize-css/dist/js/materialize.min'
```

## Add navbar

go to [navbar page](https://materializecss.com/navbar.html)

```bash
touch src/components/Navbar.vue
```

```vue
<template>
    <nav class="blue darken-3">
      <div class="nav-wrapper">
        <router-link to="/" class="brand-logo">Tasks</router-link>
        <ul class="right hide-on-med-and-down">
          <router-link
            tag="li"
            to="/"
            exact
            active-class="active">
            <a href="#">Create</a>
          </router-link>
          <router-link
            tag="li"
            to="/list"
            active-class="active">
            <a href="#">List</a>
          </router-link>
        </ul>
      </div>
    </nav>
</template>

<script>
export default {
  
}
</script>

<style scoped>
  nav {
    padding: 0 2rem;
  }
</style>
```

`App`

```vue
<template>
  <div id="app">
    <div class="container">
      <Navbar />
      <router-view/>
    </div>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar'
export default {
  components: {Navbar}
}
</script>

<style lang="scss">
  @import '~materialize-css/dist/css/materialize.min.css';
</style>

```

## Add routes

### Create views

```bash
touch src/views/List.vue src/views/Task.vue
```

### empty vue template

```vue
<template>
  <div>
    
  </div>
</template>

<script>
export default {
  
}
</script>

<style scoped>

</style>
```

### update routes

`src/router/index.js`

```js
const routes = [
  {
    path: '/',
    name: 'create',
    component: Home
  },
  {
    path: '/list',
    name: 'list',
    component: () => import('./views/List.vue')
  },
  {
    path: '/task/:id',
    name: 'task',
    component: () => import('./views/Task.vue')
  }
]
```

[time 17:25](https://www.youtube.com/watch?v=Ez5_CITkg24&t=1045s)

## Refactoring

```bash
mv src/views/Home.vue src/views/Create.vue
```

`src/router/index.js`

```js
import Create from '../views/Create.vue'
...
path: '/',
name: 'create',
component: Create
```

## Make Create page

go to [Materialize.forme/text inputs](https://materializecss.com/text-inputs.html)

```html
<div class="input-field col s6">
  <input placeholder="Placeholder" id="first_name" type="text" class="validate">
  <label for="first_name">First Name</label>
</div>
```

`Create.vue`

```vue
<template>
  <div class="row">
    <div class="col s6 offset-s3">
      <h1>Create task</h1>
      <form @submit.prevent="submitHandler">
        <div class="input-field">
          <input v-model="title" type="text" id="title" class="validate" required>
          <label for="title">Title</label>
          <span class="helper-text" data-error="Title is required" ></span>
        </div>

        <div class="chips" ref="chips"></div>

        <div class="input-field">
          <textarea v-model="description" id="description" class="materialize-textarea"></textarea>
          <label for="description">Description</label>
          <span class="character-counter" style="float:right;font-size:12px;">{{description.length}}/2048</span>
        </div>

        <input type="text" ref="datepicker">

        <button class="btn" type="submit">Create task</button>
      </form>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: 'Create',
  data: () => ({
      description: '',
      title: '',
      chips: null,
      date: null
  }),
  methods: {
    submitHandler() {
      const task = {
        title: this.title,
        description: this.description,
        id: Date.now(),
        status: 'active',
        tags: this.chips.chipsData,
        date: this.date.date
      }
      // console.log('Task', task)
      this.$store.dispatch('createTask', task)
      this.$router.push('/list')
    }
  },
  mounted() {
    this.chips = M.Chips.init(this.$refs.chips, {
      placeholder: 'Task tags'
     })
    this.date = M.Datepicker.init(this.$refs.datepicker, {
      format: 'dd.mm.yyyy',
      defaultDate: new Date(),
      setDefaultDate: true
     })
  },
  destroyed() {
    if(this.date && this.date.destroy) {
      this.date.destroy()
    }
    if(this.chips && this.chips.destroy) {
      this.chips.destroy()
    }
  }
}
</script>

```

Add font to insert crossing in chips:

`public/index.html`

```html
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
```

## Datepicker field for date

go to [materialize/forms/pickers](https://materializecss.com/pickers.html), see previous section.

## State for storing task

## Create List view

[time 45:45](https://www.youtube.com/watch?v=Ez5_CITkg24&t=2745s)

`List`

```vue
<template>
  <div>
    <h1>List</h1>
    <hr>
    <table v-if="tasks.length">
      <thead>
        <tr>
          <th>#</th>
          <th>Title</th>
          <th>Date</th>
          <th>Description</th>
          <th>Status</th>
          <th>Open</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, idx) of tasks" :key="task.id">
          <td>{{idx + 1}}</td>
          <td>{{task.title}}</td>
          <td>{{new Date(task.date).toLocaleDateString()}}</td>
          <td class="td"><div class="text">{{task.description}}</div></td>
          <td>{{task.status}}</td>
          <td>
            <router-link tag="button" class="btn btn-small" :to="'/task/' + task.id">
              Open
            </router-link>
          </td>
        </tr>
      </tbody>
    </table>
    <p v-else>
      No tasks
    </p>
  </div>
</template>

<script>
export default {
  
  computed: {
    tasks() {
      return this.$store.getters.tasks
    }
  }
}
</script>
<style scoped>
.td{
  max-width: 400px;
}
.text{
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}
</style>
```

## Create Task view

add getter in `router`

```js
taskById: s => id => s.tasks.find(t => t.id === id)
```

## Update Task

update store

```

```

[time 1:05:00](https://www.youtube.com/watch?v=Ez5_CITkg24&t=3900)

## Update status

in `Task`

```vue

```



add event on **Complete task** button.

```vue
        <button class="btn blue" type="button" @click="completeTask">Complete task</button>
```

make method:

```js
completeTask() {
  this.$store.dispatch('completeTask', this.task.id)
  this.$router.push('/list')
}
```

then in `src/store/index.js` actions:

```js
completeTask({commit}, id){
  commit('completeTask', id)
}
```

and mutations:

```js
completeTask(state, id){
  const idx = state.tasks.findIndex(i => i.id === id)
  state.tasks[idx].status = 'completed'
  localStorage.setItem('tasks', JSON.stringify(state.tasks))
}
```

### Disable buttons on completed tasks

```vue
<div v-if="task.status !== 'completed'">
  <button class="btn" type="submit" style="margin-right:1rem;">Update</button>
  <button class="btn blue" type="button" @click="completeTask">Complete task</button>
</div>
```

## Create sorting filter

from [this section on materialize page](https://materializecss.com/select.html) copy this code

```html
<div class="row">
  <div class="input-field col s6">
    <select ref="select" v-model="filter>
      <option value="" disabled selected>Choose your option</option>
      <option value="active">Option 1</option>
      <option value="outdated">Option 2</option>
      <option value="completed">Option 3</option>
    </select>
    <label>Materialize Select</label>
  </div>
</div>
```

don't forget about init:

```js
mounted() {
    M.FormSelect.init(this.$refs.select)
}
```

add **data** section:

```js
data: () => ({
  filter: null
}),
```

in computed:

```js
displayTasks(){
  return this.tasks.filter(t=> {
    if (!this.filter) {
      return true
    }
    return t.status === this.filter
  })
}
```

and change table iteration:

```vue
<tr v-for="(task, idx) of displayTasks" :key="task.id">
```

### Clear filter

Create button:

```vue
<button v-if="filter" class="btn btn-small" @click="filter = null">Clear</button>
```

## Check outdated tasks on startup

`src/store/index.js`

```js
state: {
  tasks: JSON.parse(localStorage.getItem('tasks') || '[]').map(task => {
    if(new Date(task.date) < new Date()){
      task.status = 'outdated'
    }
    return task
  })
},
```

## Build

```bash
npm run build

> vk-vue@0.1.0 build /home/varius/Documents/files/vue/minin/nuxtvuessr/vk-vue
> vue-cli-service build


⠴  Building for production...

 WARNING  Compiled with 2 warnings                                    3:07:40 PM

 warning  

asset size limit: The following asset(s) exceed the recommended size limit (244 KiB).
This can impact web performance.
Assets: 
  js/chunk-vendors.ab010058.js (318 KiB)

 warning  

entrypoint size limit: The following entrypoint(s) combined asset size exceeds the recommended limit (244 KiB). This can impact web performance.
Entrypoints:
  app (450 KiB)
      css/chunk-vendors.24ce4a6c.css
      js/chunk-vendors.ab010058.js
      css/app.92336d1e.css
      js/app.6fa4d35a.js


  File                                      Size             Gzipped

  dist/js/chunk-vendors.ab010058.js         317.97 KiB       90.95 KiB
  dist/js/app.6fa4d35a.js                   8.70 KiB         3.46 KiB
  dist/js/chunk-7e747975.9d3cd930.js        2.36 KiB         1.09 KiB
  dist/js/chunk-03367666.04f11bc0.js        2.22 KiB         1.00 KiB
  dist/service-worker.js                    1.04 KiB         0.61 KiB
  dist/precache-manifest.99c28a80fc22f78    1.03 KiB         0.38 KiB
  90c40716776916ef2.js
  dist/css/chunk-vendors.24ce4a6c.css       123.30 KiB       19.96 KiB
  dist/css/chunk-03367666.c4c079ce.css      0.12 KiB         0.11 KiB
  dist/css/chunk-7e747975.8107a90d.css      0.04 KiB         0.06 KiB
  dist/css/app.92336d1e.css                 0.04 KiB         0.05 KiB

  Images and other types of assets omitted.

 DONE  Build complete. The dist directory is ready to be deployed.
 INFO  Check out deployment instructions at https://cli.vuejs.org/guide/deployment.html

```

