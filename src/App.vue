<template>
  <div id="app">
    <div class="container">
      <div class="row">
        <div class="col-md-3 col-lg-3 col-xs-12">
          <div class="form-wrap">
            <form @submit="addRow">
              <div class="form-group ">
                <label for="InputName">Имя</label>
                <input type="text" class="form-control" id="InputName" placeholder="Имя" v-model="inputName">
              </div>
              <div class="form-group ">
                <label for="InputSurname">Фамилия</label>
                <input type="text" class="form-control" id="InputSurname" placeholder="Фамилия" v-model="inputSurname">
              </div>  
              <button type="submit" class="btn btn-success">Добавить</button>
            </form>
          </div>  
        </div>  
        <div class="col-md-9 col-lg-9 col-xs-12">
          <div class="table-wrap">
            <table class="table">
              <tr>
                <th>№</th>
                <th @click="sortBy('name'); callsCounterName++" v-model="callsCounterName">Имя</th>
                <th @click="sortBy('surname'); callsCounterSurname++" v-model="callsCounterSurname">Фамилия</th>
              </tr>
              <tr
                v-for="(user, index) in paginatedData"
                :key="index"
              >
                <td v-text="index + 1"></td>
                <td v-text="user.name"></td>
                <td v-text="user.surname"></td>
              </tr>
            </table>
              <div class="button-container">
                <button 
                    class="btn btn-outline-primary"
                    :disabled="pageNumber === 0" 
                    @click="prevPage">
                    Назад
                </button>
                <button 
                    class="btn btn-outline-primary"
                    :disabled="pageNumber >= pageCount -1" 
                    @click="nextPage">
                    Вперёд
                </button>
              </div>  
             <!-- <div class="select-container">
                <div class="input-group mb-3">
                  <div class="input-group-prepend">
                    <label class="input-group-text" for="inputGroupSelect01">Выводить по:</label>
                  </div>              
                  <select class="custom-select" id="inputGroupSelect01" v-model="selected" @change="onChangeSelected">
                    <option value='5'>5</option>
                    <option value='10'>10</option>
                    <option value='50'>50</option>
                  </select>   
                </div>           
              </div>  -->
          </div>
        </div>    
      </div>
    </div>  
  </div>
</template>

<script>

/*
  Приложение должно:
  1. Добавлять нового человека через форму
  2. При клике на th делать сортировку списка по выбраному полю
  При клике по одному и тому же полю несколько раз подряд переключать сортировку по этому полю в порядке ASC => DESC => Выкл.

  Будет круто:
  3. Пагинация
  4. select с выбором отображаемого количества элементов на странице - 5, 10, 50
*/

export default {
  name: 'app',
  data () {
    return {
      inputName:'',
      inputSurname:'',
      callsCounterName: 0,
      callsCounterSurname: 0,
      pageNumber: 0,
      selected: this.size,
      users: [
        {
          name: 'Пётр',
          surname: 'Васильев',
        }
      ],
      usersDefault: []
    }
  },

  props:{
    size:{
      type:Number,
      required:false,
      default: 5
    }
  },

  methods:{
    addRow(e){
      if(this.usersDefault.length > 0){
        this.users = this.usersDefault.slice();
        this.users.push({ name: this.inputName, surname: this.inputSurname });
        this.usersDefault = this.users.slice();
        this.inputName = '';
        this.inputSurname = '';
      }else{
        this.users.push({ name: this.inputName, surname: this.inputSurname });
        this.usersDefault = this.users.slice();
        this.inputName = '';
        this.inputSurname = '';
      }
      e.preventDefault();
    },
    sortBy(key){   
      if (this.callsCounterName == 3){
        this.callsCounterName = 0
      }
      if (this.callsCounterSurname == 3){
        this.callsCounterSurname = 0
      }
      if (key == "name" ){
        var param = this.callsCounterName;
      } else{
        var param = this.callsCounterSurname;
      }      
      switch(param){
        case 0: return this.users.sort(function(el1, el2){
            if(el1[key].toLowerCase() < el2[key].toLowerCase()){
              return -1
            }
            if (el1[key].toLowerCase() > el2[key].toLowerCase()) {
              return 1
            }         
            return 0       
          });
        case 1: return this.users.sort(function(el1, el2){  
            if(el1[key].toLowerCase() > el2[key].toLowerCase()){
              return -1
            }
            if (el1[key].toLowerCase() < el2[key].toLowerCase()) {
              return 1
            }         
            return 0       
          });
        case 2: return  this.users = this.usersDefault.slice();
        
      }       
    },
    nextPage(){
      this.pageNumber++;
    },
    prevPage(){
      this.pageNumber--;
    }
  },
  computed:{
    pageCount(){
      let l = this.users.length,
          s = this.size;
      return Math.ceil(l/s);
    },
    paginatedData(){
      const start = this.pageNumber * this.size,
            end = start + this.size;
      return this.users
              .slice(start, end);
    }
  }
}
</script>

<style>
  body{
    padding-top: 25px;
  }
  .table th {
    padding: .5rem;
    padding-top: 0;
    border-top: none;
    cursor: pointer;
  }
  .table th:first-child{
    cursor: inherit;
  }
  .custom-select{
    width: 65px;
  }
  .button-container,
  .select-container{
    display: inline-block;
    vertical-align: top;
  }
</style>
