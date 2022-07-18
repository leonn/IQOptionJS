## IQOptionJS

API em JS para comunicação com a plataforma IQOption.

Até o momento, a API foi testada nos navegadores ![Google Chrome](https://img.shields.io/badge/Google%20Chrome-4285F4?style=for-the-badge&logo=GoogleChrome&logoColor=white) ![Firefox](https://img.shields.io/badge/Firefox-FF7139?style=for-the-badge&logo=Firefox-Browser&logoColor=white)

#### Documentação
   - [Realizar login](#realizar-login)
   - [Capturar dados do perfil](#capturar-dados-do-perfil)
   - [Ver banca](#ver-banca)
   - [Ver banca atualizada](#ver-banca-atualizada)
   - [Ver todas as bancas](#ver-todas-as-bancas)
   - [Ver horario da IQOption](#ver-horario-da-iqoption)
   - [Verificar status da conexão](#verificar-status-da-conexão)
   - [Capturar velas](#capturar-velas)
   - [Abrir operação na digital](#abrir-operação-na-digital)
   - [Abrir operação na binária](#abrir-operação-na-binária)
   - [Verificar resultado de operação](#verificar-resultado-de-operação)
   - [Capturar dados de paridades](#capturar-dados-de-paridades)
   - [Capturar payout da Digital](#capturar-payout-da-digital)
   - [Capturar payout da Binaria](#capturar-payout-da-binária)
   - [Capturar operações abertas por outros dispositivos](#capturar-operações-abertas-por-outros-dispositivos)

#### Realizar Login
``` javascript
let email = 'meu@email.com';
let password = '123456';
let account_type = 'demo';

this.api = new IQOptionAPI({ email: email, password: password, account_type: account_type, });
this.api.Connect().then(async (resposta) => {
    if (resposta == null) {
        cosole.log("Não foi possível se conectar");
        this.api = null;
    } else if (resposta == false) {
        cosole.log("Voce foi desconectado");
        this.api = null;
    } else if (resposta == true) {
        cosole.log("Conectado com sucesso");
    } else {
        cosole.log("Algo de errado ocorreu, tente novamente mais tarde");
        this.api = null;
    }
});
```

 ##### Observação
 A API irá precisar de algum mecanismo para dar bypass em relação ao CORS, por padrão o IQOptionJS utiliza o [Cors Anywhere](https://cors-anywhere.herokuapp.com/), caso você não esteja conseguindo logar, tente acessar o [Cors Anywhere](https://cors-anywhere.herokuapp.com/) e verifique se você não precisa 'ativalo'.

#### Capturar dados do Perfil
``` javascript
let perfil = ths.api.Profile();
console.log(perfil);
```

### Ver banca
``` javascript
let banca = await ths.api.Balance();
console.log(banca);
```

### Ver banca atualizada
``` javascript
let banca = ths.api.BalanceNow();
console.log(banca);
```

### Ver todas as bancas
``` javascript
let bancas = ths.api.Balances();
console.log(bancas);
```

### Ver horario da IQOption
``` javascript
let tmstamp = ths.api.ServerTimestamp();
console.log(tmstamp);
```

### Verificar status da conexão
``` javascript
let statusConexao = ths.api.CheckConnect();
console.log(statusConexao);
/*
 Conectado -> true
 Não conectado -> false
 Não foi solicitado conexão -> none
*/

```

### Capturar velas
``` javascript
let paridade = 'EURUSD';
let timeframe = 60; // em segundos
let quantiaVelas = 3;
let quando = Date.now();

let velas = await this.api.GetCandles(paridade, timeframe, quantiaVelas, quando);
console.log(velas);
/*
[
    {
        at: 1658140620000000000
        close: 1.016015
        cor: "green"
        from: 1658140560
        id: 1759662
        max: 1.016035
        min: 1.015845
        open: 1.015925
        to: 1658140620
        volume: 167
    },
    ...
]
*/

```

### Abrir operação na digital
``` javascript
let paridade = 'EURUSD';
let valorEntrada = 5.0;
let direcao = 'call';
let timeframe = 1; // em minutos

let id = await this.api.BuyDigital(paridade, valorEntrada, direcao, timeframe);
```

### Abrir operação na binária
``` javascript
let paridade = 'EURUSD';
let valorEntrada = 5.0;
let direcao = 'put';
let timeframe = 1; // em minutos

let retornoOperacao = await this.api.BuyBinary(paridade, valorEntrada, direcao, timeframe);
```

### Verificar resultado de operação
Para este metodo, existem alguns argumentos extras que podem ser passados, sendo eles:
async: se passar como true, ele irá 'travar' neste metodo até receber o resultado da operação.
* Padrão: false

raw: se passar true, irá retornar os dados 'brutos' do websocket referente a operação, não irá travar internamente nem se passar o async como True.
* Padrão: false

``` javascript
let paridade = 'EURUSD';
let valorEntrada = 5.0;
let direcao = 'put';
let timeframe = 1; // em minutos
let retornoOperacao = await this.api.BuyBinary(paridade, valorEntrada, direcao, timeframe);

this.api.CheckWin(retornoOperacao.id, { async: true }).then(function (resultado) {
    if(resultado.value > 0){
        console.log('Vitoria!', resultado)
    }else if(resultado.value == 0){
        console.log('Empate!', resultado)
    }else if(resultado.value < 0){
        console.log('Derrota!', resultado)
    }
});
```

### Capturar dados de paridades
``` javascript
await this.api.GetActives().then(async function (ativos) {
    /*
    {
        // Pares das binárias com timeframe inferior a 5 minutos
        turbo: {
                    AUDCAD: true,
                    EURUSD: true,
                    ...
                },

        digital {
                    AUDCAD: true,
                    EURUSD: true,
                    ...
                },
            
        // Pares das binárias com timeframe igual ou superior a 5 minutos
        binary: {
                    AUDCAD: true,
                    EURUSD: true,
                    ...
                },
    }
    
    */        
});
```

### Capturar payout da digital
``` javascript
let payout = await this.api.GetDigitalPayout('EURUSD');
console.log(payout); // 0.8
```

### Capturar payout da binária
``` javascript
let payout = await this.api.GetBinaryPayout('EURUSD');
console.log(payout);
/*
{
    binary: 0.8,
    turbo: 0.8
}
*/

let payout = await this.api.GetBinaryPayout();
console.log(payout);
/*
{
    binary: {
                EURUSD: 0.8,
                AUDJPY: 0.8,
                ...
    },
    turbo: {
                EURUSD: 0.8,
                AUDJPY: 0.8,
                ...
    }
}
*/
```

### Capturar operações abertas por outros dispositivos
``` javascript
let operacoes = this.api.option_open_in_another_pc();
console.log(operacoes);
/*
{
    binary: {...}
    digital: {...}
}
*/
```
