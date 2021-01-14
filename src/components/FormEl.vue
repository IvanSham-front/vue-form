<template>
  <div class="container">
    <h1 class="display-4 text-center">Создать нового пользователя</h1>

    <form v-on:submit.prevent="addClient" class="form-row col-md-8">
      <div class="form-group col-md-6">
        <label for="newClientName">Имя</label>

        <input type="text" class="form-control input-sm" id="newClientName" 
        v-model="form.name"
        :class="$v.form.name.$error ? 'is-invalid' : ''"
        >

        <span class="text-danger" 
        v-if="$v.form.name.$dirty && !$v.form.name.required"
        >{{msgRequired}}</span>

        <span class="text-danger" 
        v-if="$v.form.name.$dirty && !$v.form.name.minLength"
        >{{msgMinLengthText}}<br></span>

        <span class="text-danger" 
        v-if="$v.form.name.$dirty && !$v.form.name.alpha"
        >{{msgAlpha}}</span> 
      </div>

      <div class="form-group col-md-6">
        <label for="newClientSurname">Фамилия</label>

        <input type="text" class="form-control" id="newClientSurname" 
        :class="$v.form.surname.$error ? 'is-invalid' : ''"
        v-model="form.surname">
          
        <span class="text-danger" 
          v-if="$v.form.surname.$dirty && !$v.form.surname.required"
        >{{msgRequired}}<br></span>

        <span class="text-danger" 
          v-if="$v.form.surname.$dirty && !$v.form.surname.minLength"
        >{{msgMinLengthText}}<br></span>

        <span class="text-danger" 
          v-if="$v.form.surname.$dirty && !$v.form.surname.alpha"
        >{{msgAlpha}}</span>
      </div>


      <div class="form-group col-3">
        <label for="newClientDateBirth">Дата рождения</label>

        <input type="date" class="form-control" v-model="form.dateBirth"
        :class="$v.form.dateBirth.$error ? 'is-invalid' : ''"
        >
        
        <span class="text-danger" 
          v-if="$v.form.dateBirth.$dirty && !$v.form.dateBirth.required"
        >{{msgRequired}}</span>
      </div>


      <div class="form-group col-4">
        <label for="newClientName">Номер телефона</label>

        <input type="number" class="form-control" id="newClientName" 
        :class="$v.form.phoneNumber.$error ? 'is-invalid' : ''"
        v-model="form.phoneNumber"
        >
      
        <span class="text-danger" 
          v-if="$v.form.phoneNumber.$dirty && !$v.form.phoneNumber.required"
        >{{msgRequired}}<br></span>

        <span class="text-danger" 
          v-if="($v.form.phoneNumber.$dirty && !$v.form.phoneNumber.maxLength) ||
          ($v.form.phoneNumber.$dirty && !$v.form.phoneNumber.minLength) ||
          ($v.form.phoneNumber.$dirty && !$v.form.phoneNumber.numeric) ||
          ($v.form.phoneNumber.$dirty && !$v.form.phoneNumber.isPhone)"
        >{{msgNum}}</span>
      </div>

      <div class="form-group col-5">
        <label for="newClientEmail">Электронная почта</label>

        <input type="email" class="form-control" id="newClientEmail" 
        :class="$v.form.email.$error ? 'is-invalid' : ''"
        v-model="form.email">
      
        <span class="text-danger" 
          v-if="$v.form.email.$dirty && !$v.form.email.required"
        >{{msgRequired}}</span>

        <span class="text-danger" 
        v-if="$v.form.email.$dirty && !$v.form.email.email"
        >{{msgEmail}}</span>
      </div>

      <div class="form-check col-sm-10">
          <input type="checkbox" class="form-check-input" id="acceptSendSMS" checked v-model="form.acceptSendSMS">

          <label for="acceptSendSMS" class="form-ceck-label">Отправить смс для верификации</label>
      </div>

      <div class="form-group row">
        <div class="col-sm-6">
          <button class="btn btn-success" type="submit">Отправить</button>
        </div>
      </div> 
    </form>

    <div class="table-responsive">
      <h2 class="display-4 text-center">Список зарегистрированных пользователей</h2>
     
      <table class="table">
        <thead>
          <tr>
            <th>Имя</th>
            <th>Фамилия</th>
            <th>Электронная почта</th>
            <th>Номер телефона</th>
            <th>Дата рождения</th>
            <th>Верифицирован</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="(client, id) in clients"
          :key="client.id"
          >
            <td>{{client.name}}</td>
            <td>{{client.surname}}</td>
            <td>{{client.email}}</td>
            <td>{{client.phoneNumber}}</td>
            <td>{{client.dateBirth}}</td>
            <td v-if="client.acceptSendSMS === true">Да</td>
            <td v-if="client.acceptSendSMS === false">Нет</td>

            <div class="btn-group btn-grop-sm">
                <button class="btn btn-danger" type="button" v-on:click="deleteClient(id)">Удалить</button>
            </div>
          </tr>
        </tbody>  
      </table>
    </div>
  </div>
</template>


<script>
import { validationMixin } from 'vuelidate'
import { required, minLength, numeric, maxLength, email, helpers} from 'vuelidate/lib/validators'
import { isPhone } from "../components/validators";

const alpha = helpers.regex('alpha', /^[a-zA-Zа-яёА-ЯЁ]*$/)


export default {
  name: 'FormEl',
  mixins: [validationMixin],
  validations: {
    form: {
      name: {
        required,
        minLength: minLength(2),
        alpha
      },
      surname: {
        required,
        minLength: minLength(2),
        alpha
      },
      email: {
        email,
        required
      },
      phoneNumber: {
        numeric,
        required,
        maxLength: maxLength(11),
        minLength: minLength(11),
        isPhone
      },
      dateBirth: {
        required
      }
    }
  },
  data() {
    return {
      msgAlpha: 'В поле не должны быть числа и спец.символы',
      msgRequired: 'Поле обязательное для заполнения',
      msgEmail: 'Адрес электронной почты должен содеражить символ "@"!',
      msgMinLengthText: 'Минимальное кол-во символов - 2',
      msgNum: 'Номер телефона должен начинаться с 7 и содержать 11 цифр',
      clients: [ 
      ],
      form: {
        id: 0,
        name: '',
        surname: '',
        email: '',
        phoneNumber: '7',
        dateBirth: '',
        acceptSendSMS: true,
      },
      editStatus: false
    }
  },
  methods: {
    addClient() {
      this.$v.form.$touch()
      if (this.$v.form.$invalid || this.$v.form.$error) {
        this.form.submitStatus = 'ERORR'
      } else {
        this.form.submitStatus = 'OK';
        let count = this.form.id;
        count++;
        let client = {
          id: count,
          name: this.form.name,
          surname: this.form.surname,
          email: this.form.email,
          phoneNumber: this.form.phoneNumber,
          dateBirth: this.form.dateBirth,
          acceptSendSMS: this.form.acceptSendSMS,
        }
        this.clients.push(client);
        this.form.name = '';
        this.form.surname = '';
        this.form.email = '';
        this.form.phoneNumber = '7';
        this.form.dateBirth = '';
      }
    },
    deleteClient(index) {
      this.clients.splice(index, 1)
    },
    editClient(id) {
      this.client
    }

  }
};
</script>

<style scoped>
h1, h2 {
  font-size: 2.5rem;
}

form {
  padding: 1em;
  margin: 0 auto;
}

input[type='number'] {
    -moz-appearance:textfield;
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
    -webkit-appearance: none;
}

.form-check-input {
  position: relative;
  margin-left: 0;
  margin-right: .5em;
}

</style>  