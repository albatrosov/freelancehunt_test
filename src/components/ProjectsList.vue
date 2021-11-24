<template>
  <h1>Freelancehunt Test Task</h1>

  <div class="search">
    <div class="search__filter">
      <span>Поиск по тексту: </span>
      <input type="text" v-model="searchValue">
    </div>
    <div class="search__sort">
      <span>Сортировка: </span>
      <a href="#" @click.prevent="sort, this.isSorted = false">Обычная</a>
      <a href="#" @click.prevent="sort, this.isSorted = true">Наименьший бюджет</a>
    </div>
  </div>

  <div class="projects">
    <div class="project" 
          v-for="{ id, attributes: project } in projects" 
          :key="id" 
          :data-id="id" 
          @click="showProjectDetails"
    >
    <!-- data-id - для передачи в запрос отдельного проекта -->
      <h2>
        {{ project.name }}
      </h2>
      <div class="project__description" v-show="searchValue.length > 2">
        {{
          '...' + 
          project.description.slice(
            project.description.indexOf(searchValue) - 20, 
            project.description.indexOf(searchValue) + 20)
          + '...' 
        }}
        <!-- 
          в задании не было указано, что нужно показывать описание проекта сразу,
          поэтому изначально оно скрыто. 
          Но чтобы было понятно, что поиск происходит и по описанию, в процессе поиска оно появляется.

          Это довольно примитивная версия, 
          можно улучшать до красивого показа части текста, ограниченного полными словами.
          Проработать случай, когда нет совпадений в описании, а только в заголовке
          (чтобы не показывать шесть точек, если в описании не совпало) и т.д.
         -->
      </div>
      <ul>
        <h3>Навыки</h3>
        <li v-for="{ id, name } in project.skills" :key="id">
          {{ name }}
        </li>
      </ul>
      <div class="project__budget" v-if="project.budget">
        <span>
          {{ project.budget.amount }}
        </span>
        <sup>
          {{ project.budget.currency }}
        </sup>
      </div>
    </div>

  </div>

  <!-- нет особо знаний как правильно разделять на компоненты и собирать всё вместе, 
  получилось разве что таким образом -->
  <Modal v-if="projectDetails.data" 
         v-show="modalIsOpen" 
         :projectDetails="projectDetails.data"
         @click="showProjectDetails">
  </Modal>

  <span class="author">by Lebedev</span>
</template>

<script>

import Modal from './Modal.vue'

export default {

  components: {
    Modal
  },

  data() {
    return {
      fetchedProjects: [],
      projectDetails: {},
      modalIsOpen: false,
      searchValue: '',
      isSorted: false,
    }
  },

  methods: {

    showProjectDetails(e) {  
      let headers = new Headers();
      headers.append("Authorization", "Bearer b10b705f3e505fdc2a82ef88ebc1e5062e5686a8");

      let requestOptions = {
        method: 'GET',
        headers: headers,
        redirect: 'follow'
      };

      fetch(`https://api.freelancehunt.com/v2/projects/${e.currentTarget.dataset.id}`, requestOptions)
      .then(response => response.json())
      .then(response => Object.assign(this.projectDetails, response));    

      this.modalIsOpen = !this.modalIsOpen;
    },

    sort(array) {
      return array.sort((a, b) => {
        [a, b] = [a.attributes.budget, b.attributes.budget]

        a === null ? a : a = a.currency === 'RUB' ? a.amount / 3 : a.amount
        b === null ? b : b = b.currency === 'RUB' ? b.amount / 3 : b.amount
        

        return (a === null) - (b === null) || a - b;        
      });
    },

    filter(arr) {
      return arr.filter((project) => {  
        return project.attributes.name.toLowerCase().includes(this.searchValue.toLowerCase()) ||
               project.attributes.description.toLowerCase().includes(this.searchValue.toLowerCase())
      });
    }


  },

  computed: {

    projects() {
      let filteredProjects = [],
          sortedProjects = []
      Object.assign(filteredProjects, this.fetchedProjects);

      if(this.searchValue) {
        filteredProjects = this.filter(filteredProjects);
      } 
      if(this.isSorted) {
        Object.assign(sortedProjects, this.sort(filteredProjects));
      }

      return this.isSorted ? sortedProjects : filteredProjects
    }
  },

  created() {

    let headers = new Headers();
    headers.append("Authorization", "Bearer b10b705f3e505fdc2a82ef88ebc1e5062e5686a8");

    let requestOptions = {
      method: 'GET',
      headers: headers,
      redirect: 'follow'
    };

    fetch("https://api.freelancehunt.com/v2/projects", requestOptions)
      .then(response => response.text())
      .then(result => { 
        this.fetchedProjects = JSON.parse(result).data;
      })
      .catch(error => console.log('error', error));
  }
}

</script>

<style scoped>
h1 {
  font-size: 36px;
  margin-bottom: 80px;
}

h2{
  display: block;
  width: 100%;
  margin-top: 0;
  font-size: 26px;
  border-bottom: 1px solid transparent;
  transition: color .2s ease;
}

a {
  color: #42b983;
}

.search {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 20px;

}
.search span {
  font-size: 18px;
  font-weight: 500;
}

.search__filter input {
  margin-left: 10px;
  padding: 4px 10px;
  border: 1px solid #cacaca;
  border-radius: 4px;
  font-size: 18px;
}

.search__filter input:focus {
  border-color: #66afe9;
  outline: none;
}

.search__sort a {
  display: inline-block;
  margin-left: 10px;
  text-decoration: none;
}

.search__sort a:hover {
  text-decoration: underline;
}

.projects {
  display: flex;
  align-items: stretch;
  justify-content: space-between;
  flex-wrap: wrap;
  margin-bottom: 80px;
}

.project {
  display: flex;
  flex-wrap: wrap;
  width: 49%;
  margin-bottom: 20px;
  padding: 20px 25px;
  box-sizing: border-box;
  text-align: left;
  border: 1.5px solid #00ae5a;
  border-radius: 10px;
  cursor: pointer;
}

.project:nth-child(odd) {
  margin-right: .5%;
}
.project:nth-child(even) {
  margin-left: .5%;
}

.project:hover h2{
  color: #00ae5a;
}

.project__description {
  width: 100%;
}

.project__budget {
  width: 50%;
  align-self: flex-end;
  text-align: right;
  color: #00ae5a;
}

.project__budget span {
  font-size: 24px;
  font-weight: 600;
}

.project__budget sup {
  font-size: 18px;
  margin-left: 5px;
}

.project ul {
  align-self: flex-end;
  width: 50%;
  padding: 0;
  margin-bottom: 0;
  list-style: none;
}

.project ul li {
  display: block;
  margin: 4px 0;
}

.project ul li:before {
  content: '— ';
}

.project ul li:last-child {
  margin-bottom: 0;
}

.author {
  display: block;
  margin-bottom: 20px;
  text-align: center;
  font-size: 14px;
}

@media(max-width: 991px) {

  .search {
    flex-wrap: wrap;
    justify-content: flex-start;
    text-align: left;;
  }

  .search span {
    display: block;
    margin-bottom: 10px;
  }

  .search__filter, .search__sort {
    width: 100%;
  }

  .search input, .search a {
    margin-left: 0;
    margin-right: 10px;
  }
  
  .search__filter {
    margin-bottom: 20px;
  }
}

@media(max-width: 767px) {

  h1 {
    font-size: 26px;
    margin-bottom: 40px;
  }

  h2 {
    font-size: 22px;
  }

  h3 {
    margin-top: 0;
  }

  .project {
    width: 100%;
  }

  .project:nth-child(odd), .project:nth-child(even) {
    margin: 0 0 15px;
  }

  .project ul, .project__budget {
    width: 100%;
  }

  .project__budget {
    margin-top: 20px;
  }
}
</style>
