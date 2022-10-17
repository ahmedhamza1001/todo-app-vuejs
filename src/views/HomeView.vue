<template>
  <transition name="overlay" appear v-if="showModalSucc" @click="showModalSucc = 0">
    <div class="overlay-div">
    </div>
  </transition>
  <transition name="slide" appear v-if="showModalSucc">
    <div class="real-modal col-5 bg-white">
      <div class="d-flex justify-content-center" style="padding-top: 50px;">
        <svg style="width: 35px;fill: #4caf50;    margin-right: 18px;" xmlns="http://www.w3.org/2000/svg"
             viewBox="0 0 512 512">
          <path
            d="M256 512c141.4 0 256-114.6 256-256S397.4 0 256 0S0 114.6 0 256S114.6 512 256 512zM369 209L241 337c-9.4 9.4-24.6 9.4-33.9 0l-64-64c-9.4-9.4-9.4-24.6 0-33.9s24.6-9.4 33.9 0l47 47L335 175c9.4-9.4 24.6-9.4 33.9 0s9.4 24.6 0 33.9z"/>
        </svg>
        <h4>{{ titleModal }}</h4>
      </div>
    </div>
  </transition>

  <div class="home mb-4">
    <div v-if="showForm" class="card col-lg-6 mx-auto p-3 mt-5">
      <div class="addForm">
        <h4 class="d-flex align-items-center justify-content-between"
            style="color: white; font-size: 18px; justify-content: center;">
          <span>
            Add New Item
            <svg style="width: 20px;fill:white;margin-left: 13px;" xmlns="http://www.w3.org/2000/svg"
                 viewBox="0 0 512 512">
              <path
                d="M256 512c141.4 0 256-114.6 256-256S397.4 0 256 0S0 114.6 0 256S114.6 512 256 512zM232 344V280H168c-13.3 0-24-10.7-24-24s10.7-24 24-24h64V168c0-13.3 10.7-24 24-24s24 10.7 24 24v64h64c13.3 0 24 10.7 24 24s-10.7 24-24 24H280v64c0 13.3-10.7 24-24 24s-24-10.7-24-24z"/>
            </svg>
          </span>
          <span class="viewTodoItems" @click="showForm = 0">
            View Todo Items
          </span>
        </h4>

        <div class="form-groub">
          <input type="text" v-model.trim="name" class="form-control" placeholder="Name">
        </div>
        <div class="form-groub mt-3">
          <input type="text" v-model.trim="description" class="form-control" placeholder="Description">
        </div>
        <div class="form-groub mt-3">
          <input type="date" v-model.trim="date" class="form-control" placeholder="e.g. Make A video">
        </div>

        <div class="form-groub">
          <span
            style="float: left; margin-top: 15px; color: white; font-size: 13px;color:#bbcadf">Pick a Category</span><br>

          <div class="d-flex mt-1">
            <div class="col-6 p-2">
              <div class="wrapper" style="min-height: 110px;background: #bbcadf; padding: 10px; border-radius: 7px;">
                <button class="radioBTN busisnessInput"
                        style="border: 1px solid #46529d;box-shadow: #46529d 0px 10px 36px 0px, #46529d 0px 0px 0px 1px;"
                        :class="[activeCat == 1 ? 'active' : '']" @click="setActiveCat('busisness')"></button>
                <br>
                <label style="text-align: center !important;">Busisness</label>
              </div>
            </div>
            <div class="col-6 p-2">
              <div class="wrapper" style="min-height: 110px;background: #bbcadf; padding: 10px; border-radius: 7px;">
                <button class="radioBTN personalInput"
                        style="border: 1px solid #e741a2;box-shadow: #e741a2 0px 10px 36px 0px, #e741a2 0px 0px 0px 1px;"
                        :class="[activeCat == 2 ? 'active' : '']" @click="setActiveCat('personal')"></button>
                <br>
                <label style="text-align: center !important;">Personal</label>
              </div>
            </div>
          </div>

          <span v-if="updating" @click="updateTodo" class="btn btn-primary form-control addTodo">Update Item</span>
          <span v-else @click="addTodo" class="btn btn-primary form-control addTodo">Add Todo</span>
        </div>

      </div>
    </div>

    <div v-else class="card col-lg-6 mx-auto p-3 mt-5">
      <div class="addForm">
        <h4 class="d-flex align-items-center justify-content-between"
            style="color: white; font-size: 18px; justify-content: center;">
          <span>
            Todo Items
          </span>
          <span class="viewTodoItems" @click="showForm = 1;showDropDown = 0">
            Add New Item
          </span>
        </h4>

        <hr style="height: 2px; background: #f5deb38a;">

        <ul v-if="todoItems.length !== 0">

          <!--filter-->
          <li class="filter" style="width: 23%; margin-left: auto;">
            <p class="badge badge-danger mb-0" @click="showDropDown = !showDropDown"
               style="cursor:pointer;width: 100%;font-size: 13px; color: white; padding: 11px 46px; border-radius: 30px; text-align: center; background: #ff9800; font-weight: bold;">
              FILTER
            </p>
            <transition name="dropdownHand" v-if="showDropDown" appear>
              <ul id="ulFilter">
                <p class="badge badge-danger mb-0" @click="filterType = 1;showDropDown = 0">
                  Completed
                </p>
                <p class="badge badge-danger mb-0" @click="filterType = 2;showDropDown = 0">
                  UnCompleted
                </p>
                <p class="badge badge-danger mb-0" @click="filterType = 3;showDropDown = 0">
                  Both
                </p>
              </ul>
            </transition>
          </li>

          <!--todo items here-->
          <li v-for="item in getFilteredTodoItems" :key="item.id" :id="item.id"
              class="mb-1 d-flex align-items-center mt-3"
              style="justify-content: space-between;background: #bbcadf; padding: 11px 7px 1px 22px; border-radius: 13px; text-align: left;">
            <div class="left">
              <h6 style=";color: #46529d; font-weight: 500; font-size: 22px;margin-bottom: 3px !important">
                {{ item.name }}
              </h6>
              <p style="font-size: 15px; color: #4e4f54f5;margin-bottom: 4px;word-break: break-all; max-width: 73%;">
                {{ item.description }}
              </p>
              <p class="badge badge-danger"
                 style="font-size: 11px; color: white; background: #3f51b5; padding: 6px 15px; border-radius: 30px; text-align: center;">
                {{ item.date }}
              </p>
              <p class="badge badge-danger"
                 :style="'margin-left: 8px; font-size: 11px; color: white; padding: 6px 15px; border-radius: 30px; text-align: center;background: ' + [item.category == 'Personal' ? '#00bcd4;' : '#e91e63;']">
                {{ item.category }}
              </p>
            </div>
            <div class="right d-flex align-items-baseline">

              <span v-if="!item.complete" @click="todoCompelete(item.id)">
                <svg
                  style="cursor: pointer; fill: rgb(47 154 91); margin-right: 10px; border: 1px solid rgb(47 154 91); width: 31px; padding: 5px; border-radius: 50%;"
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 512 512">
                <path
                  d="M374.6 86.6c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L192 178.7l-57.4-57.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3l80 80c12.5 12.5 32.8 12.5 45.3 0l160-160zm96 128c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L192 402.7 86.6 297.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3l128 128c12.5 12.5 32.8 12.5 45.3 0l256-256z"/>
               </svg>
              </span>
              <p v-else class="badge badge-danger"
                 style="margin-right: 10px !important; font-size: 10px; color: white; padding: 6px 15px; border-radius: 30px; text-align: center;background:#2cbf70">
                <span @click="unCompletedItem(item.id)">COMPELTED</span>
              </p>
              <span>
                <svg @click="updateTodoItem(item.id)" style="cursor: pointer;fill: #020b2a;width: 20px;"
                     xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512"><!--! Font Awesome Pro 6.2.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. --><path
                  d="M471.6 21.7c-21.9-21.9-57.3-21.9-79.2 0L362.3 51.7l97.9 97.9 30.1-30.1c21.9-21.9 21.9-57.3 0-79.2L471.6 21.7zm-299.2 220c-6.1 6.1-10.8 13.6-13.5 21.9l-29.6 88.8c-2.9 8.6-.6 18.1 5.8 24.6s15.9 8.7 24.6 5.8l88.8-29.6c8.2-2.8 15.7-7.4 21.9-13.5L437.7 172.3 339.7 74.3 172.4 241.7zM96 64C43 64 0 107 0 160V416c0 53 43 96 96 96H352c53 0 96-43 96-96V320c0-17.7-14.3-32-32-32s-32 14.3-32 32v96c0 17.7-14.3 32-32 32H96c-17.7 0-32-14.3-32-32V160c0-17.7 14.3-32 32-32h96c17.7 0 32-14.3 32-32s-14.3-32-32-32H96z"/></svg>
              </span>

              <span @click="deleteItem(item.id)" style="cursor: pointer;margin-left: 10px;margin-right: 10px;">
                <svg style="fill: #4f0606;width: 20px;" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512"><!--! Font Awesome Pro 6.2.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. --><path
                  d="M135.2 17.7C140.6 6.8 151.7 0 163.8 0H284.2c12.1 0 23.2 6.8 28.6 17.7L320 32h96c17.7 0 32 14.3 32 32s-14.3 32-32 32H32C14.3 96 0 81.7 0 64S14.3 32 32 32h96l7.2-14.3zM32 128H416V448c0 35.3-28.7 64-64 64H96c-35.3 0-64-28.7-64-64V128zm96 64c-8.8 0-16 7.2-16 16V432c0 8.8 7.2 16 16 16s16-7.2 16-16V208c0-8.8-7.2-16-16-16zm96 0c-8.8 0-16 7.2-16 16V432c0 8.8 7.2 16 16 16s16-7.2 16-16V208c0-8.8-7.2-16-16-16zm96 0c-8.8 0-16 7.2-16 16V432c0 8.8 7.2 16 16 16s16-7.2 16-16V208c0-8.8-7.2-16-16-16z"/></svg>
              </span>
            </div>
          </li>
        </ul>

        <ul v-else>
          <li class="mb-3 d-flex align-items-center"
              style="justify-content: space-between;background: #bbcadf; border-radius: 13px; text-align: left;padding:10px;">
            <span>No Items Added Yet!!</span>
          </li>
        </ul>

        <div class="pagination justify-content-center" v-if="todoItems.length">
          <ul class="ulPagination d-flex">
            <svg @click="prevPage" style="cursor: pointer;width: 30px;fill: #e91e63;margin-right: 10px;"
                 xmlns="http://www.w3.org/2000/svg"
                 viewBox="0 0 512 512">
              <path
                d="M512 256C512 114.6 397.4 0 256 0S0 114.6 0 256S114.6 512 256 512s256-114.6 256-256zM271 135c9.4-9.4 24.6-9.4 33.9 0s9.4 24.6 0 33.9l-87 87 87 87c9.4 9.4 9.4 24.6 0 33.9s-24.6 9.4-33.9 0L167 273c-9.4-9.4-9.4-24.6 0-33.9L271 135z"/>
            </svg>

            <li @click="updatePage(page)" v-for="page in numPages" :key="page">
              <span :class="'badge badge-info '+ [currentPage == page ? 'active' : '']">
                {{ page }}
              </span>
            </li>

            <svg @click="nextPage" style="cursor: pointer;width: 30px;fill: #e91e63;margin-left: 10px;"
                 xmlns="http://www.w3.org/2000/svg"
                 viewBox="0 0 512 512">
              <path
                d="M0 256C0 397.4 114.6 512 256 512s256-114.6 256-256S397.4 0 256 0S0 114.6 0 256zM241 377c-9.4 9.4-24.6 9.4-33.9 0s-9.4-24.6 0-33.9l87-87-87-87c-9.4-9.4-9.4-24.6 0-33.9s24.6-9.4 33.9 0L345 239c9.4 9.4 9.4 24.6 0 33.9L241 377z"/>
            </svg>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import $ from 'jquery'

export default {
  name: 'HomeView',
  data () {
    return {
      filterType: 3,
      showDropDown: 0,
      showForm: 0,
      activeCat: 0,
      name: '',
      description: '',
      date: '',
      showModalSucc: 0,
      todoItems: [],
      allTodoItemsForComputed: [],
      updating: 0,
      titleModal: '',
      // for pagination
      numPages: 0,
      itemPerPage: 5,
      currentPage: 1
    }
  },
  computed: {
    getFilteredTodoItems () {
      this.getTodoItems()
      if (this.filterType === 1) {
        return this.allTodoItemsForComputed.filter(item => item.complete === true)
      }

      if (this.filterType === 2) {
        return this.allTodoItemsForComputed.filter(item => item.complete === false)
      }
      return this.todoItems
    }
  },
  methods: {
    async getTodoItems () {
      await axios.get('http://localhost:3000/items').then(res => {
        this.allTodoItemsForComputed = res.data
      })
    },
    async todoCompelete (itemID) {
      let item = null
      await axios.get('http://localhost:3000/items/' + itemID).then(res => {
        item = res.data
        item.complete = true
      })
      await axios.put('http://localhost:3000/items/' + itemID, item).then(res => {
        if (res.status === 200) {
          this.titleModal = 'Updated Successfully!'
          this.showModalSucc = 1

          setTimeout(() => {
            this.showModalSucc = 0
          }, 1500)
        }
      })
      this.updateItems()
    },
    async unCompletedItem (itemID) {
      let item = null
      await axios.get('http://localhost:3000/items/' + itemID).then(res => {
        item = res.data
        item.complete = false
      })
      await axios.put('http://localhost:3000/items/' + itemID, item).then(res => {
        if (res.status === 200) {
          this.titleModal = 'Updated Successfully!'
          this.showModalSucc = 1

          setTimeout(() => {
            this.showModalSucc = 0
          }, 1500)
        }
      })
      this.updateItems()
    },
    async updateTodoItem (itemID) {
      this.updating = itemID
      await axios.get('http://localhost:3000/items/' + itemID).then(res => {
        this.name = res.data.name
        this.description = res.data.description
        this.date = res.data.date
        const category = res.data.category
        if (category === 'Personal') {
          this.activeCat = 2
        } else {
          this.activeCat = 1
        }

        this.showForm = 1
      })
    },
    async updateItems () {
      await axios.get('http://localhost:3000/items').then(res => {
        if ((res.data.length % this.itemPerPage) === 0 &&
          this.currentPage === this.numPages && this.currentPage % 2 !== 0) {
          this.currentPage -= 1
        }
        this.numPages = Math.ceil(res.data.length / this.itemPerPage)
        this.todoItems = res.data.slice((this.currentPage - 1) * this.itemPerPage, this.currentPage * this.itemPerPage)
      })
    },
    async deleteItem (itemID) {
      await axios.delete('http://localhost:3000/items/' + itemID).then(res => {
        if (res.status === 200) {
          $('#' + itemID).css('background', 'yellow')
          setTimeout(() => {
            this.titleModal = 'Deleted Successfully!'
            this.showModalSucc = 1
            $('#' + itemID).hide(600)
          }, 500)
          setTimeout(() => {
            this.showModalSucc = 0
          }, 2500)
          setTimeout(() => {
            this.updateItems()
          }, 1000)
        }
      })
    },
    async addTodo () {
      if (this.activeCat !== 0 || this.name.length !== 0 || this.description.length !== 0 || this.date.length !== 0) {
        const cat = this.activeCat === 1 ? 'Busisness' : 'Personal'
        const item = {
          name: this.name,
          description: this.description,
          date: this.date,
          complete: false,
          category: cat
        }
        await axios.post('http://localhost:3000/items', item).then(res => {
          if (res.status === 201) {
            this.name = ''
            this.description = ''
            this.date = ''
            this.activeCat = 0
            this.titleModal = 'Added Successfully!'
            this.showModalSucc = 1
          }
        })
        setTimeout(() => {
          this.showForm = 0
          this.showModalSucc = 0
          this.updateItems()
        }, 1500)

        this.updateItems()
      }
    },
    setActiveCat (activeCat) {
      if (activeCat === 'personal') {
        this.activeCat = 2
      } else {
        this.activeCat = 1
      }
    },
    nextPage () {
      if (this.currentPage !== this.numPages) {
        this.currentPage += 1
      }
      this.updateItems()
    },
    prevPage () {
      if (this.currentPage !== 1) {
        this.currentPage -= 1
      }
      this.updateItems()
    },
    updatePage (pageNumber) {
      this.currentPage = pageNumber
      this.updateItems()
    },
    async updateTodo () {
      const cat = this.activeCat === 1 ? 'Busisness' : 'Personal'
      const item = {
        name: this.name,
        description: this.description,
        date: this.date,
        category: cat
      }

      await axios.put('http://localhost:3000/items/' + this.updating, item).then(res => {
        if (res.status === 200) {
          this.titleModal = 'Updated Successfully!'
          this.name = ''
          this.description = ''
          this.date = ''
          this.activeCat = 0
          this.showModalSucc = 1
        }

        setTimeout(() => {
          this.showForm = 0
          this.showModalSucc = 0
          this.updating = 0
          this.updateItems()
        }, 1500)
      })
    }
  },
  async mounted () {
    await axios.get('http://localhost:3000/items').then(res => {
      this.numPages = Math.ceil(res.data.length / this.itemPerPage)
      this.todoItems = res.data.slice((this.currentPage - 1) * this.itemPerPage, this.currentPage * this.itemPerPage)
    })
  }
}
</script>
<style>
#ulFilter {
  background: white;
  border-radius: 4px;
  margin-top: 6px;
  margin-bottom: 10px;
  padding: 0;
  overflow: hidden;
}

#ulFilter p {
  cursor: pointer;
  font-size: 13px;
  color: rgb(255, 152, 0);
  border-radius: 0px;
  text-align: center;
  font-weight: bold;
  border-bottom: 1px solid rgb(255, 152, 0);
  padding: 14px;
  width: 100%;
  transition: all 0.2s ease-in-out;
}

#ulFilter p:hover {
  font-size: 14px;
  color: white;
  border-radius: 0px;
  border-bottom: 1px solid rgb(255, 152, 0);
  padding: 11px;
  background-color: rgb(255, 152, 0);
}

.ulPagination li span,
.ulPagination li svg {
  background: none;
  border: 1px solid #e91e63;
  border-radius: 50%;
  color: #e91e63;
  fill: #e91e63;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  font-size: 14px;
  padding: 9px;
  height: 30px;
  font-size: 14px;
  padding: 9px;
  margin: 4px;
  cursor: pointer;
  transition: all 0.3s ease-in-out;
}

.ulPagination li span.active,
.ulPagination li span:hover {
  background: #e91e63;
  border-radius: 50%;
  widtbackground: #e91e63;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  font-size: 14px;
  padding: 9px;
  h: 30px;
  height: 30px;
  font-size: 14px;
  padding: 9px;
  margin: 4px;
  color: white;
  transform: scale(1.2);
}

.overlay-div {
  position: fixed;
  z-index: 99999;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  background-color: #000000;
  opacity: 0.6;
}

.real-modal {
  min-height: 140px;
  text-align: center;
  border-radius: 13px;
  box-shadow: rgb(0 0 0 / 16%) 0px 10px 36px 0px, rgb(0 0 0 / 6%) 0px 0px 0px 1px;
  position: fixed;
  z-index: 99999;

  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.slide-enter-active,
.slide-leave-active {
  transition: transform 0.6s;
}

.slide-enter-from,
.slide-leave-to {
  transform: translateY(-200vh) translateX(-50%);
}

.dropdownHand-enter-active,
.dropdownHand-leave-active {
  transition: opacity 1.1s;
}

.dropdownHand-enter-from,
.dropdownHand-leave-to {
  opacity: 0;
}

input[type='text'], input[type='date'] {
  background: none;
  border: none;
  height: 40px;
  border-bottom: 2px solid rgba(187, 202, 223, 0.18);
  border-radius: 0;
  color: #bbcadf;
  font-size: 15px;
}

input[type='text']:focus, input[type='date']:focus {
  background: none;
  border: none;
  height: 40px;
  border-bottom: 2px solid rgba(187, 202, 223, 0.13);
  border-radius: 0;
  color: #bbcadf;
  outline: none !important;
  font-size: 15px;
}

::placeholder {
  color: #bbcadf !important;
  font-size: 15px;
}

.viewTodoItems {
  background: #bbcadf;
  padding: 8px 15px;
  font-size: 14px;
  border-radius: 30px;
  color: #46529d;
  transition: all 0.3s ease-in-out;
  cursor: pointer;
}

.viewTodoItems:hover {
  border: 1px solid #bbcadf;
  background-color: #46529d;
  color: #bbcadf;
  font-size: 15px;

}

.card {
  background-color: #46529d;
}

ul {
  list-style: none;
  padding-left: 7px;
}

.addTodo {
  background-color: #e741a2;
  border: 1px solid #e741a2;
  height: 40px;
  font-size: 16px;
  margin-top: 15px;
}

.addTodo:hover {
  background-color: #46529d;
  border: 1px solid #e741a2;
  color: #e741a2;
  font-size: 17px;
}

button {
  height: 20px;
  width: 20px;
  border-radius: 50%;
  margin-top: 20px;
  transition: all 0.2s ease-in-out;
}

.radioBTN:hover {
  background: #e741a2;
  border: 2px solid #621889;
  transform: scale(1.1);
}

.active {
  background: #e741a2;
  border: 2px solid #621889;
}
</style>
