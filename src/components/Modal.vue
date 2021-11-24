<template>
  <div>
    <div class="modal_overlay" @click="showProjectDetails"></div>
    <div class="modal">
      <h2>
        <a href="">
          {{ projectDetails.attributes.name }}
        </a>
      </h2>
      <div class="modal__description" v-html="projectDetails.attributes.description_html"></div>
      <div class="modal__date">
        <span>Дата публикации: </span>
        <span>
          
          {{ 
            this.date.getDate() + '-' +
            this.date.getMonth() + '-' +
            this.date.getFullYear() + ' в ' +
            this.date.getHours() + ':' +
            this.date.getMinutes()
          }}
        </span>
      </div>
      <a class="modal__link" :href="projectDetails.links.self.web">Ссылка на Freelancehunt</a>
    </div>
  </div>   
</template>

<script>
  export default {
    props: ['projectDetails'],

    data() {
      return {
        date: new Date(this.projectDetails.attributes.published_at)
      }
    }
  }
</script>

<style scoped>
.modal {
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  width: 600px;
  max-width: 90vw;
  max-height: 90vh;
  overflow: auto;
  padding: 40px 25px;
  box-sizing: border-box;
  border: 1px solid #000;
  background: #fff;
  z-index: 3;
  border-radius: 10px;
}

.modal_overlay {
  position: fixed;
  left: 0;
  top: 0;
  width: 100vw;
  height: 100vh;
  background: #000;
  opacity: .6;
  z-index: 2;
}

.modal h2 {
  font-size: 28px;
  margin-top: 0;
}

.modal a {
  text-decoration: none;
  transition: color .2s ease;
}

.modal a:hover {
  color: #42b983;
}

.modal h2 a {
  color: #000;
}

.modal__description {
  font-size: 18px;
  text-align: left;
}

.modal__date span:first-child {
  font-weight: 600;
}

.modal__date {
  text-align: left;
}

.modal__link {
  display: block;
  margin-top: 40px;
  font-weight: 600;
  font-size: 22px;
  color: #00ae5a;
}

@media(max-width: 767px) {

  .modal {
    max-height: 70vh;
  }

  .modal h2 {
    font-size: 24px;
  }

  .modal__description{
    font-size: 16px;
  }

  .modal__date span{
    display: block;
  }
}
</style>
