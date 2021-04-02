<template>
    <div class="card">
        <h5 class="card-header">API Clients</h5>
        <div class="card-body">
            <div class="row">
                <div v-for="client in clients" class="col-sm-6 mb-2">
                    <div class="card">
                        <div class="card-body">
                            <h5 class="card-title">{{ client.name }}</h5>
                            <p class="card-subtitle"><b>Client ID:</b> {{ client.id }}</p>
                            <p class="card-text"><b>Client Secret:</b> {{ client.secret }}</p>
                            <a href="#"  @click.prevent="editClient(client)" class="btn btn-secondary"><span class="fa fa-pen"></span> Edit</a>
                            <a href="#" @click.prevent="deleteKey(client.id)" class="btn btn-danger"><span
                                class="fa fa-trash"></span> Delete</a>
                        </div>
                    </div>
                </div>
            </div>
            <div class="row mt-3 ml-1">

                <!-- Small modal -->
                <button type="button" class="btn btn-primary" @click="createClient">
                    <span class="fa fa-plus"></span> Create Client
                </button>

                <div class="modal fade bd-example-modal-sm" tabindex="-1" role="dialog" id="modal"
                     aria-labelledby="mySmallModalLabel" aria-hidden="true">
                    <div class="modal-dialog modal-sm">
                        <div class="modal-content">
                            <div class="modal-header">
                                <h5 class="modal-title" id="exampleModalLabel">{{ (isUpdate)?'Update Client':'Add New Client' }}</h5>
                                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                    <span aria-hidden="true">&times;</span>
                                </button>
                            </div>
                            <div class="modal-body">
                                <form>
                                    <div class="form-group">
                                        <label for="recipient-name" class="col-form-label">Name:</label>
                                        <input type="text" v-model="newItemName" class="form-control"
                                               id="recipient-name">
                                    </div>
                                    <span v-for="errorsList in requestErrors" class="text-danger">
                                        <template v-for="error in errorsList">
                                            {{ error }}
                                        </template>
                                    </span>
                                </form>
                            </div>
                            <div class="modal-footer">
                                <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                                <button type="button" @click="(isUpdate)?updateClient(currentClient.id):addNewClient" class="btn btn-primary">Save</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "ApiClientIndex",
    data: () => {
        return {
            clients: {},
            newItemName: '',
            redirectUrl: 'http://example.com/callback',
            requestErrors: {},
            isUpdate:false,
            currentClient: {}
        }
    },
    mounted() {
        this.getData();
    },
    methods: {
        getData() {
            axios.get('/oauth/clients')
                .then(response => {
                    this.clients = response.data;
                    console.log(response.data);
                });
        },
        createClient(){
            this.currentClient = {};
            this.newItemName = '';
            this.isUpdate = false;
            $('#modal').modal('show');
        },
        addNewClient() {
            this.requestErrors = {};
            axios.post('/oauth/clients', {
                name: this.newItemName,
                redirect: this.redirectUrl
            }).then(response => {
                $('#modal').modal('hide');
                this.newItemName = '';
                this.getData();
                this.requestErrors = {};
            }).catch(error => {
                this.requestErrors = error.response.data.errors;
                // List errors on response...
            });
        },
        editClient(client){
            this.currentClient = client;
            this.newItemName = client.name;
            this.isUpdate = true;
            $('#modal').modal('show');
        },
        updateClient(clientId) {
            this.requestErrors = {};
            axios.put('/oauth/clients/'+clientId, {
                name: this.newItemName,
                redirect: this.redirectUrl
            }).then(response => {
                $('#modal').modal('hide');
                this.newItemName = '';
                this.getData();
                this.requestErrors = {};
                this.isUpdate = false;
            }).catch(error => {
                this.requestErrors = error.response.data.errors;
                // List errors on response...
            });
        },
        deleteKey(clientId) {
            if (confirm("Are you sure?")) {
                axios.delete('/oauth/clients/' + clientId)
                    .then(response => {
                        this.getData();
                    });
            }
        }
    }
}
</script>

<style scoped>

</style>
