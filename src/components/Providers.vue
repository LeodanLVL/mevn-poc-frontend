<script>
import { checkCompatEnabled } from '@vue/compiler-core';

const url2 = "http://localhost:4000/api/"

export default {
    data() {
        return {
            url: 'http://localhost:4000/api/providers',
            url2,
            providers: [],
            buttonName: 'Insert',
            providersChecked: [],
            name: '',
            providerEdit: {}

        }
    },
    props: ['providersI', 'clients', 'idProvider'],
    async mounted() {
        try {
            if (this.idProvider != false) {
                this.providersChecked = [];
                let listEdit = [];
                await fetch(this.url).then((resp) => {
                    return resp.json();
                }).then(({ data }) => {
                    //console.log(data);
                    this.providers = [...data]
                });
                const requestClient = async (url2) => {
                    try {
                        const req = await fetch(url2 + `clients/${this.idProvider}`);
                        const resp = await req.json();
                        const { data } = resp;
                        // console.log(data);
                        // console.log(this.client.providers);

                        listEdit = [...data.providers];
                        loadChecked(listEdit);
                    } catch (error) {
                        console.log(error);
                    }
                }
                requestClient(url2);
                
                const loadChecked = (listEdit) => {


                    this.providers.forEach(element => {
                        // console.log(this.listEdit);
                        let prov = {};
                        let checked = false;
                        if (!!listEdit[0]) {
                            listEdit.forEach(elementP => {
                            
                            if (element.id == elementP._id) {
                                checked = true;
                            } 

                        });
                        if (checked==true) {
                                prov = {
                                    id: element.id,
                                    name: element.name,
                                    checked: true
                                }
                            } else {
                                prov = {
                                    id: element.id,
                                    name: element.name,
                                    checked: false
                                }
                            }
                        }else{
                            prov = {
                                    id: element.id,
                                    name: element.name,
                                    checked: false
                                }
                        }
                        


                        this.providersChecked.push(prov);

                    });
                }

            } else {
                await fetch(this.url)
                    .then((resp) => {
                        return resp.json();
                    }).then(({ data }) => {
                        //console.log(data);
                        this.providers = [...data]
                    });
                this.providersChecked = [];
                this.providers.forEach(element => {

                    const prov = {
                        id: element.id,
                        name: element.name,
                        checked: false
                    }
                    this.providersChecked = [...this.providersChecked, prov]
                });
            }


        } catch (error) {
            console.log(error);
        }
    },
    watch: {
        providersChecked: {
            handler: function () {
                // if (this.providersI.next) {

                //     while (this.providersI.next) {
                //         this.providersI.pop();
                //     }
                // }
                this.providersI.splice(0);

                this.providersChecked.forEach(element => {
                    if (element.checked == true) {
                        const provider = {
                            id: element.id,
                            name: element.name
                        }
                        this.providersI.push(provider)
                    }
                });

            }, deep: true
        }
    },
    methods: {
        insertupdate() {
                
            if (this.name.trim() != '') {
                    
                 if (this.buttonName == 'Update') {
                    
                        const putF = async () => {
                        try {
                            const insertF = await fetch(`${this.url}/update/${this.providerEdit.id}`, {
                                method: 'PUT',
                                headers: { 'Content-Type': 'application/json' },
                                body: JSON.stringify({
                                    name: this.name
                                })
                            }).then(function (response) {
                                return response.json();

                            }).then(function (data) {
                                // console.log(data);
                            });

                            this.providersChecked.forEach(element => {
                                if (element.id == this.providerEdit.id) {
                                    element.name = this.name;
                                }
                            });
                            this.clients.forEach(element => {

                                element.providers.forEach(elementP => {

                                    if (elementP._id == this.providerEdit.id||elementP.id == this.providerEdit.id) {
                                        elementP.name = this.name;

                                    }
                                });
                            });
                            this.name = "";
                            this.providerEdit = {};
                            this.buttonName = "Insert"
                            // console.log(this.clients);
                            // this.refresh(this.clients);
                        } catch (error) {
                            console.log(error);
                        }

                    }
                    putF();
                    
                } else {
                    //////////////////////////////////
                    const postF = async () => {
                        let dataValue = {};
                        try {
                            const insertF = await fetch(`${this.url}/new`, {
                                method: 'POST',
                                headers: { 'Content-Type': 'application/json' },
                                body: JSON.stringify({
                                    name: this.name
                                })
                            }).then(function (response) {
                                return response.json();

                            }).then(function (data) {
                                // console.log(data);
                                dataValue = data;
                            });
                            // console.log(dataValue.data.id);
                            this.providersChecked = [...this.providersChecked, { name: this.name, id: dataValue.data.id, checked: false }]

                            this.name = "";
                            this.providerEdit = {};
                            this.buttonName = "Insert"

                        } catch (error) {
                            console.log(error);
                        }

                    }
                    postF();
                }
            }else{

                if (this.buttonName == 'Update') {
                    this.name = "";
                            this.providerEdit = {};
                            this.buttonName = "Insert"
                }
            }

        },
        editClick(provider) {
            this.providerEdit = provider;
            this.buttonName = 'Update'
        },
        async deleteP(id) {
            try {
                const deleteFetch = await fetch(`${this.url}/delete/${id}`, { method: 'DELETE' })
                    .then(
                    // console.log("deleted")
                );
                this.providersChecked.forEach((element, i) => {
                    if (element.id == id) {
                        this.providersChecked.splice(i, 1);
                    }
                });
                this.clients.forEach((element, i) => {
                    element.providers.forEach((elementP, e) => {
                        if (elementP._id == id||elementP.id == id) {
                            element.providers.splice(e, 1);

                        }

                    });
                });
            } catch (error) {
                console.log(error);
            }
        }
    }
};
</script>

<template>
    <div class="input">
        <p class="elements">Provider:</p>
        <input class="elements" @input="e => this.name = e.target.value" type="text" :value="providerEdit.name"
            name="provider">
        <button @click="insertupdate" class="elements">{{ buttonName }}</button>
        <div class="containerProviders">
            <div v-for="provider in this.providersChecked" class="provider">
                <input class="elements" type="checkbox" :value="provider.checked" v-model="provider.checked"
                    :name="provider.name" :id="provider.id">
                <p class="elements">{{ provider.name }}</p>
                <button class="elements" @click="editClick(provider)">edit</button>
                <button class="elements" @click="deleteP(provider.id)">delete</button>
            </div>
        </div>
    </div>
</template>

<style scoped>
.elements {
    display: inline-block;
    margin-right: 5px;
}
</style>