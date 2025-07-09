<template>
    <div>
        <Message 
            :msg="msg" 
            v-show="msg"
        />
        <div>
            <form id="burguer-form" @submit="createBurguer">
                <div id="form-elements">
                    <div class="input-container">
                        <label for="nome">Nome do cliente:</label>
                        <input type="text" id="nome" name="nome" v-model="nome" placeholder="Digite o seu nome">
                    </div>
                    <div class="input-container">
                        <label for="pao">Escolha o pão:</label>
                        <select name="pao" id="pao" v-model="pao">
                            <option value="">Selecione o seu pão</option>
                            <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">
                                {{ pao.tipo }}
                            </option>
                        </select>
                    </div>
                    <div class="input-container">
                        <label for="carne">Escolha a carne do seu burguer:</label>
                        <select name="carne" id="carne" v-model="carne">
                            <option value="">Selecione o tipo de carne</option>
                            <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">
                                {{ carne.tipo }}
                            </option>
                        </select>
                    </div>
                    <div id="opcionais-container">
                        <label id="opcionais-title" for="opcionais">Selecione os opcionais:</label>
                        <div class="checkbox-container" v-for="opcional in opcionais_data" :key="opcional.id">
                            <input id="checkbox-opcionais" type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo">
                            <label for="checkbox-opcionais">{{ opcional.tipo }}</label>
                        </div>
                    </div>
                    <div class="input-container">
                        <input type="submit" class="submit-btn" value="Criar meu burguer">
                    </div>
                </div>
            </form>
            <p class="erro-msg" v-if="erro">{{ erro }}</p>
        </div>
    </div>
</template>

<script>
    import Message from './Message.vue';

    export default {
        name: 'BurguerForm',
        components: {
            Message
        },
        data() {
            return {
                paes: null,
                carnes: null,
                opcionais_data: null,
                nome: null,
                pao: null,
                carne: null,
                opcionais: [],
                status: "Solicitado",
                msg: null,
                erro: ""
            }
        },
        methods: {
            async getIngredientes() {
                try {
                    const req = await fetch("http://localhost:3000/ingredientes")
                    const data = await req.json()

                    //resgatando os dados da api
                    this.paes = data.paes
                    this.carnes = data.carnes
                    this.opcionais_data = data.opcionais
                }
                catch(e) {
                    console.log('Erro ao resgatar os dados!')
                }
            },
            async createBurguer(e) {
                e.preventDefault()
                
                try{
                    const data = {
                        nome: this.nome,
                        carne: this.carne,
                        pao: this.pao,
                        opcionais: Array.from(this.opcionais),
                        status: "Solicitado"
                    }

                    const dataJson = JSON.stringify(data)

                    if(!nome.value || !carne.value || !pao.value) {
                        this.erro = "⚠️ Por favor, preencha todos os campos obrigatórios."
                        setTimeout(() => this.erro = "", 3000)
                        return;
                    }
                 
                    const req = await fetch("http://localhost:3000/burgers",{
                        method: "POST",
                        headers: { "Content-Type": "application/json" },
                        body: dataJson
                    })

                    const res = await req.json()

                    //colocar uma msg de sistema
                    this.msg = `Pedido N°${res.id} realizado com sucesso!`
                    
                    //limpar msg    
                    setTimeout(() => this.msg = "", 3000)
                }
                catch(e) {
                    console.log("Erro ao criar o burguer!")
                }

                //limpar os campos
                this.nome = ""
                this.carne = ""
                this.pao = ""
                this.opcionais = ""
            }
        },
        mounted() {
            this.getIngredientes()
        }
    }
</script>

<style scoped>
    #form-elements { 
        display: flex;
        flex-direction: column;
        padding: 1.6rem;
        border: 1px solid rgba(221, 221, 221, .8);
        border-radius: 4px;
    }
    #burguer-form {
        max-width: 400px;
        margin: 0 auto;
    }
    .input-container {
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }
    label[for="nome"], 
    label[for="pao"], 
    label[for="carne"], 
    label[for="opcionais"] {
        font-weight: bold;
        margin-bottom: 15px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #fcba03;
    }
    input, select {
        padding: 5px 10px;
        width: 300px;
    }
    #opcionais-container {
        display: flex;
        flex-wrap: wrap;
        margin-bottom: 20px;
    }
    #opcionais-title {
        width: 100%;
        margin-bottom: 1.6rem;
    }
    .checkbox-container {
        display: flex;
        align-items: flex-start;
        width: 50%;
        margin-bottom: 20px;
    }
    .checkbox-container label,
    .checkbox-container input {
        width: auto;
    }
    .checkbox-container label {
        margin-left: 6px;
        font-weight: bold;
    }
    .submit-btn {
        background-color: #222;
        color: #fcba03;
        font-weight: bold;
        text-align: center;
        border: 2px solid #222;
        border-radius: 6px;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }
    .submit-btn:hover {
        background-color: transparent;
        color: #222;
    }
    .erro-msg {
        background-color: #ffe6e6;
        color: #d40000;
        padding: 1rem;
        margin-top: 1rem;
        border-radius: 5px;
        font-weight: bold;
    }


</style>