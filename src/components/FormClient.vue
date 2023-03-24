<script>
import Providers from './Providers.vue'


const url = "http://localhost:4000/api/"

export default {


    name: 'FormClient',
    data() {
        return {
            client: '', nameI: '', emailI: '', phoneI: '', providersI: [{}], url
        }
    },
    props: ['id', "btn", "clients"]
    ,
    components: {
        Providers
    },
    mounted() {

        if (this.id != false) {
            // console.log(id);

            const requestClient = async (url) => {
                try {
                    const req = await fetch(url + `clients/${this.id}`);
                    const resp = await req.json();
                    const { data } = resp;
                    // console.log(data);
                    this.client = data;
                    this.nameI = this.client.name;
                    this.emailI = this.client.email;
                    this.phoneI = this.client.phone;
                    // console.log(this.client.providers);
                    this.providersI = [...resp.data.providers];
                    this.showProvider = true;
                    // console.log(this.providersI);
                } catch (error) {
                    console.log(error);
                }
            }
            requestClient(url);

        }

    },
    methods: {
        close() { this.$emit('close'); },
        // refresh(vari) { this.$emit('refresh', vari) },
        insertUpate() {
            if (this.nameI != "" && this.emailI != "" && this.phoneI != "") {
                if (this.nameI != null && this.emailI != null && this.phoneI != null) {

                    if (this.btn == "Update") {
                        const providersId = [];
                        this.providersI.forEach(element => {

                            providersId.push({
                                _id: element.id
                            })

                        });
                        const putF = async (url) => {
                            try {
                                const insertF = await fetch(`${url}clients/update/${this.id}`, {
                                    method: 'PUT',
                                    headers: { 'Content-Type': 'application/json' },
                                    body: JSON.stringify({
                                        name: this.nameI,
                                        email: this.emailI,
                                        phone: this.phoneI,
                                        providers: providersId
                                    })
                                }).then(function (response) {
                                    return response.json();

                                }).then(function (data) {
                                    // console.log(data);
                                });

                                this.clients.forEach((element, i) => {
                                    if (element.id == this.id) {
                                        // this.clients[i] = {
                                        //     name: this.nameI,
                                        //     email: this.emailI,
                                        //     phone: this.phoneI,
                                        //     providers: this.providersI
                                        // }
                                        element.name = this.nameI;
                                        element.email = this.emailI;
                                        element.phone = this.phoneI;
                                        element.providers = this.providersI;

                                    }

                                });
                                // console.log(this.clients);
                                // this.refresh(this.clients);
                            } catch (error) {
                                console.log(error);
                            }

                        }
                        putF(url);


                    } else {

                        const providersId = [];
                        this.providersI.forEach(element => {

                            providersId.push({
                                _id: element.id
                            })

                        });
                        const post = async (url) => {
                            try {
                                let datavalue = {};
                                const insertF = await fetch(`${url}clients/new`, {
                                    method: 'post',
                                    headers: { 'Content-Type': 'application/json' },
                                    body: JSON.stringify({
                                        name: this.nameI,
                                        email: this.emailI,
                                        phone: this.phoneI,
                                        providers: providersId

                                        // {_id:"640c23347463a0c79c94f841"}

                                    })
                                }).then(function (response) {
                                    return response.json();

                                }).then(function (data) {
                                    // console.log(data);
                                    datavalue = data;
                                });
                                this.clients.push({
                                    name: this.nameI,
                                    email: this.emailI,
                                    phone: this.phoneI,
                                    providers: this.providersI,
                                    id: datavalue.data.id
                                });
                                // this.refresh(this.clients);

                            } catch (error) {
                                console.log(error);
                            }

                        }
                        post(url);

                    }
                }
            }
        },
        async deleteFasync() {
            try {
                const deleteFetch = await fetch(`${url}clients/delete/${this.id}`, { method: 'DELETE' })
                    .then(

                    // console.log("deleted")
                );

                this.clients.forEach((element, i) => {
                    if (element.id == this.id) {
                        this.clients.splice(i, 1);

                    }
                });


            } catch (error) {
                console.log(error);
            }
        }

    }
}

</script>
<template>
    
    <div class="overlay">


        <div class="formulario">


            <div class="container-formulario">

                <div class="input">
                    <p>Name:</p>
                    <input @input="e => this.nameI = e.target.value" type="text" :value="client.name" name="name">
                </div>
                <div class="input">
                    <p>Email:</p>
                    <input @input="e => this.emailI = e.target.value" type="text" :value="client.email" name="email">
                </div>
                <div class="input">
                    <p>Phone:</p>
                    <input @input="e => this.phoneI = e.target.value" type="text" :value="client.phone" name="phone">
                </div>
                <Providers :providersI="providersI" :idProvider="id" :clients="clients"></Providers>
                <div class="btn-container">
                    <button class="btn" v-if="btn == `Update`" @click="deleteFasync(); close()">delete</button>
                    <button class="btn" @click="close">Cancelar</button>
                    <button class="btn" @click="insertUpate(); close()">{{ btn }}</button>
                </div>
                <!-- <div class="input"></div> -->
            </div>

        </div>
    </div>
</template>



<style>
.overlay {
    position: absolute !important;
    width: 100%;
    height: 100%;
    background-color: rgb(0, 0, 0, 0.77);
    z-index: 10;
    display: flex;
    justify-content: center;
    align-items: center;
    
}
</style>
<style scoped>
div .formulario {
    border-radius: 10px;
    background-color: white;
    width: 70%;
    height: 70%;
    display: flex;
    justify-content: center;
    align-items: center;
}

.close {
    position: relative;
    right: 10%;
    top: 10%;
}

.container-formulario {

    width: 70%;
}

.input {
    margin: 1rem 0;
}

.input p {
    font-size: 1.3rem;
    margin-bottom: 5px;
}

.input input {
    border: 0.5px solid rgba(0, 0, 0, 0.3);
    outline: none;
    height: 40px;
    padding: 10px;
    max-width: 600px;
    font-size: 1rem;
    width: 100%;
    border-radius: 5px;

}

.btn-container {
    display: flex;
    justify-content: flex-start;
}

.btn {
    margin: 0 5px;
    padding: 10px 20px;
    font-size: 1rem;
    text-transform: uppercase;
    border-radius: 5px;
    border: none;
}

.btn:hover {
    cursor: pointer;
    box-shadow: 3px 3px 10px rgba(0, 0, 0, 0.5);
}
</style>

