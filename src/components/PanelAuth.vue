<template>
<div>
  <section v-if="errored">
    <p>We're sorry, we're not able to retrieve this information at the moment, please try back later</p>
  </section>

  <section v-else>   

    <div class="block_center">

      <select class="select_style"
      v-model="idWindow">
        <option v-for="(win,i) in windows" :key="i" :value="win.id_wnd">
          Окно {{ win.name_wnd }} {{win.fio_operator}}
        </option>
      </select>

      <select v-model="idOper" class="select_style">
        <option v-for="(oper, i) in operators" :key="i" :value="oper.id_operator">
          {{oper.fio_operator}}
        </option>
      </select>    

      <input class="input_style"
      v-model="password" placeholder="Введите пароль">

      <button 
      class="select_style"
      v-on:click="authorize">
      Старт
      </button>     

    </div>    
  </section>
</div>
  
</template>

<script>
import md5 from 'md5';

export default {  
  data() {
    return {
      errored: false,
      windows: [],
      operators: [],
      password: "",
      idOper: "",
      idWindow: "",
      MD5password: ""

    };
  },

  created(){
    try {
      this.getOperators();
      this.getWindows();
    } catch (error) {
      console.error('error: ', error);
      this.errored = true;
    }
    
  },
  methods:{
    async getOperators(){
      const response = await fetch('http://193.161.214.165:11888/pg_queue_operator_list/request', 
        { 
          method: 'POST', 
          body: JSON.stringify({ apiKey: 'ygyhghkjjkghxrzawedfh7654fhukjkki', ip: '192.168.1.222' }) 
        });      
      const result = await response.json();
      this.operators = result.operators.operators;
    },

    async getWindows(){
      const response = await fetch('http://193.161.214.165:11888/pg_queue_window_list/request', 
        { 
          method: 'POST', 
          body: JSON.stringify({ apiKey: 'ygyhghkjjkghxrzawedfh7654fhukjkki', ip: '192.168.1.222' }) 
        });      
      const result = await response.json();
      this.windows = result.windows.windows;
    },

    authorize(){
      this.setMD5password();
      this.sendData();
    },

    setMD5password(){
      this.MD5password = md5(this.password);
    },

    async sendData(){
      const response = await fetch('http://193.161.214.165:11888/pg_queue_auth/request', 
        { 
          method: 'POST', 
          body: JSON.stringify({ parameter1:this.MD5password, id_wnd: this.idWindow, apiKey: 'ygyhghkjjkghxrzawedfh7654fhukjkki', id_operator: this.idOper })
        });      
      const result = await response.json();

      if(result.success === true){
        alert("success: " + result.description[0].message + ", active: " + result.active);
      } else{
        alert("unsuccess: " + result.description[0].message + ", active: " + result.active);
      }
    }

  }
}
</script>


<style>
.select_style{
  padding: 0.5rem;
  margin: 0.3rem;
  width: 20rem;  
}

.input_style{
  padding: 0.5rem;
  margin: 0.3rem;
  width: 18.7rem; 
}

.block_center{
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

</style>