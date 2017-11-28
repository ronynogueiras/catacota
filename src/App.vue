<template>
  <div id="app">
    <h1>Cotação</h1>
    <h6>Fonte: http://api.promasters.net.br/cotacao/v1/valores</h6>
    <h5>Atualizado em: {{ time }}</h5>
    <p style="text-align: center;">
      <button type="button" class="btn" :disabled="loading" @click.prevent="update = update + 1">{{ loadingLabel }}</button>
    </p>
    <div  v-if="!loading">
      <div class="ui two stackable cards">
        <div class="ui card">
          <br>
          <span class="title-x-size">Real</span>
          <br>
          <span class="title-xx-size">Valor: R$1,00</span>
          <br>
        </div>
        <div class="ui card">
          <br>
          <span class="title-x-size">{{ name }}</span>
          <br>
          <span class="title-xx-size">Valor: {{ usd | currency }}</span>
          <br>
        </div>
      </div>
    </div>
    <div v-else>
      <div class="spinner">
        <div class="rect1"></div>
        <div class="rect2"></div>
        <div class="rect3"></div>
        <div class="rect4"></div>
        <div class="rect5"></div>
      </div>
    </div>
    <br>
    <br>
    <p>
      Copyright (c) 2017 Rony Nogueira - All Rights Reserved.
    </p>
  </div>
</template>

<script>
import moment from 'moment';
import numeral from 'numeral';

export default {
  name: 'app',
  data () {
    return {
      usd: 0.00,
      name: '',
      time: '',
      update: 0,
      loading: false,
      loadingLabel: 'Atualizar',
    }
  },
  watch: {
    update: 'get'
  },
  methods: {
    get() {
      this.loading = true;
      this.loadingLabel = 'Atualizando...';
      fetch('http://api.promasters.net.br/cotacao/v1/valores?moedas=USD&alt=json').then((res) => {
        const contentType = res.headers.get('content-type');
        if (contentType && contentType.indexOf('application/json') !== -1) {
          res.json().then((json) => {
            this.name = json.valores.USD.nome;
            this.usd = json.valores.USD.valor;
            this.time = moment.unix(json.valores.USD.ultima_consulta).format('L [as] LTS');
            this.loading = false;
            this.loadingLabel = 'Atualizar';
          })
        }
      }).catch((err) => {
        this.loading = false;
        this.loadingLabel = 'Atualizar';
        console.log(err);
      });
    }
  },
  created() {
    moment.locale('pt-br');
    this.get();
    window.setInterval(() => {
      this.update = this.update + 1;
    }, 1000 * 60 * 2);
  },
  filters: {
    currency(value) {
      return numeral(value).format('$0,0.0000');
    },
  },
}
</script>

<style>
body {
  overflow-y: hidden;
  overflow-x: hidden;
  margin: 0 40px;
  width: 100wh;
	height: 90vh;
	background: linear-gradient(-45deg, #40F085, #12D4A9, #23A6D5, #23D5AB);
	background-size: 400% 400%;
	-webkit-animation: Gradient 15s ease infinite;
	-moz-animation: Gradient 15s ease infinite;
	animation: Gradient 15s ease infinite;
}
@-webkit-keyframes Gradient {
	0% {
		background-position: 0% 50%
	}
	50% {
		background-position: 100% 50%
	}
	100% {
		background-position: 0% 50%
	}
}

@-moz-keyframes Gradient {
	0% {
		background-position: 0% 50%
	}
	50% {
		background-position: 100% 50%
	}
	100% {
		background-position: 0% 50%
	}
}

@keyframes Gradient {
	0% {
		background-position: 0% 50%
	}
	50% {
		background-position: 100% 50%
	}
	100% {
		background-position: 0% 50%
	}
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
h1, h5, h6 {
  color: #fff
}
.title-x-size {
  font-size: 62px;
}
.title-xx-size {
  font-size: 90px;
}
.spinner {
  margin: 100px auto;
  width: 50px;
  height: 40px;
  text-align: center;
  font-size: 10px;
}

.spinner > div {
  background-color: #333;
  height: 100%;
  width: 6px;
  display: inline-block;

  -webkit-animation: sk-stretchdelay 1.2s infinite ease-in-out;
  animation: sk-stretchdelay 1.2s infinite ease-in-out;
}

.spinner .rect2 {
  -webkit-animation-delay: -1.1s;
  animation-delay: -1.1s;
}

.spinner .rect3 {
  -webkit-animation-delay: -1.0s;
  animation-delay: -1.0s;
}

.spinner .rect4 {
  -webkit-animation-delay: -0.9s;
  animation-delay: -0.9s;
}

.spinner .rect5 {
  -webkit-animation-delay: -0.8s;
  animation-delay: -0.8s;
}

@-webkit-keyframes sk-stretchdelay {
  0%, 40%, 100% { -webkit-transform: scaleY(0.4) }
  20% { -webkit-transform: scaleY(1.0) }
}

@keyframes sk-stretchdelay {
  0%, 40%, 100% {
    transform: scaleY(0.4);
    -webkit-transform: scaleY(0.4);
  }  20% {
    transform: scaleY(1.0);
    -webkit-transform: scaleY(1.0);
  }
}
.btn {
  padding: 20px 35px;
  background-color: #fff;
  border-radius: 8px;
  font-weight: 700;
}
.btn:hover {
  background-color: #e2e2e2;
  cursor: pointer;
}
</style>
