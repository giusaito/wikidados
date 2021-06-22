<template>
    <q-page padding>
        <div class="row justify-end">
            <div class="col-lg-4 col-md-6 text-right">
                <q-btn color="primary" text-color="white" glossy unelevated icon="person_add" label="Cadastrar Usuário" class="q-mb-md" @click="cadastrarUsuario()" />
            </div>
        </div>
        <q-markup-table>
            <thead>
                <tr>
                <th>Id</th>
                <th>Avatar</th>
                <th>Nome</th>
                <th>Email</th>
                <th>Excluir</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(usuario, index) in jsonUsuarios" :key="index">
                    <td class="text-center">{{usuario.id}}</td>
                    <td class="text-center">
                        <q-avatar>
                            <img :src="usuario.avatar">
                        </q-avatar>
                    </td>
                    <td class="text-center">
                        <a href="javascript:void(0)" class="text-weight-bold text-primary inline-block" @click="editarUsuario(usuario.id)">
                            {{usuario.first_name}} {{usuario.last_name}}
                        </a>
                    </td>
                    <td class="text-center">{{usuario.email}}</td>
                    <td class="text-center">
                        <q-btn flat icon="delete" style="background: #d8505f; color: white" @click="excluirUsuario(usuario.id)" />
                    </td>
                </tr>
            </tbody>
        </q-markup-table>

        <div class="q-pa-lg flex flex-center">
            <q-pagination
                    v-model="pagina"
                    :min="paginaAtual" 
                    :max="totalPaginas"
                    :input="irParaNovaPagina(pagina)"
                    direction-links
                    boundary-links
                    icon-first="skip_previous"
                    icon-last="skip_next"
                    icon-prev="fast_rewind"
                    icon-next="fast_forward"
            >
            </q-pagination>
        </div>

        <q-dialog v-model="registerModal" @hide="cancelarEdicaoUsuario">
            <q-card style="width: 700px; max-width: 80vw;">
                <q-card-section>
                    <div class="text-h6">Cadastrar Usuário</div>
                </q-card-section>
                <q-card-section class="q-pt-none">
                    <q-input outlined v-model="usuario.first_name" label="Nome" class="q-mb-md" />
                    <q-input outlined v-model="usuario.last_name" label="Sobrenome" class="q-mb-md" />
                    <q-input outlined v-model="usuario.email" label="Email" class="q-mb-md" type="email" />
                </q-card-section>
                <q-card-actions align="center" class="bg-white text-teal">
                    <q-btn label="Cadastrar Usuário" style="background: #44B1A7; color: white" @click="salvarUsuario('cadastrar')" v-close-popup />
                    <q-btn style="background: #d8505f; color: white" label="Cancelar" v-close-popup />
                </q-card-actions>
            </q-card>
        </q-dialog>
        <q-dialog v-model="editModal" @hide="cancelarEdicaoUsuario"
        >
            <q-card style="width: 700px; max-width: 80vw;" v-if="usuario !== null">
                <q-card-section>
                    <div class="text-h6">Usuário: {{usuario.first_name}} {{usuario.last_name}}</div>
                </q-card-section>

                <q-card-section class="q-pt-none">
                    <q-input outlined v-model="usuario.first_name" label="Nome" class="q-mb-md" />
                    <q-input outlined v-model="usuario.last_name" label="Sobrenome" class="q-mb-md" />
                    <q-input outlined v-model="usuario.email" label="Email" class="q-mb-md" type="email" />
                </q-card-section>

                <q-card-actions align="center" class="bg-white text-teal">
                    <q-btn label="Editar Usuário" style="background: #44B1A7; color: white" @click="salvarUsuario('editar')" v-close-popup />
                    <q-btn style="background: #d8505f; color: white" label="Cancelar" v-close-popup />
                </q-card-actions>
            </q-card>
        </q-dialog>
        <q-dialog v-model="alert">
            <q-card>
                <q-card-section>
                    <div class="text-h6">Erro</div>
                </q-card-section>
                <q-card-section class="q-pt-none">
                    Algo deu errado! Por favor, tente novamente!
                </q-card-section>
                <q-card-actions align="right">
                    <q-btn flat label="OK" color="primary" v-close-popup />
                </q-card-actions>
            </q-card>
        </q-dialog>
    </q-page>
</template>
<script>
import { api } from 'src/boot/axios'
export default {
  data() {
    return {
      jsonUsuarios: [],
      totalPaginas: null,
      pagina:1,
      paginaAtual: 1,
      porPagina: null,
      registerModal: false,
      editModal: false,
      alert: false,
      usuario: null,
      editingIndex: null
    }
  },
    methods: {
        excluirUsuario(id) {
            this.$q.dialog({
                title: 'Excluir Usuário',
                message: 'Você tem certeza que deseja excluir este usuário?',
                cancel: true,
                persistent: true
            }).onOk(() => {
                api.delete(`/users/${id}`);
                var index = this.jsonUsuarios.findIndex(e => e.id === id)
                if (index > -1) {
                    this.jsonUsuarios.splice(index, 1)
                }
            })
        },
        cadastrarUsuario() { 
            this.usuario = {
                'email':'',
                'first_name':'',
                'last_name':'',
                'avatar':''
            };
            this.registerModal = true
        },
        editarUsuario(id) {
            var index = this.jsonUsuarios.findIndex(e => e.id === id)
            if (index > -1) {
                this.editingIndex = index
                this.usuario = JSON.parse(JSON.stringify(this.jsonUsuarios[index]))
                this.editModal = true
            } else {
                this.alert = true;
            }
        },
        cancelarEdicaoUsuario() {
            this.usuario = null
            this.editingIndex = null
        },
        salvarUsuario(tipo) {
            if(tipo == 'cadastrar'){
                api.post(`/users`, 
                    {
                        id: `${this.usuario.id}`, 
                        email: `${this.usuario.email}`, 
                        first_name: `${this.usuario.first_name}`, 
                        last_name: `${this.usuario.last_name}`, 
                        avatar: `${this.usuario.avatar}`, 
                    }
                );
            }
            else if(tipo == 'editar'){
                api.put(`/users/${this.usuario.id}`, 
                    {
                        id: `${this.usuario.id}`, 
                        email: `${this.usuario.email}`, 
                        first_name: `${this.usuario.first_name}`, 
                        last_name: `${this.usuario.last_name}`, 
                        avatar: `${this.usuario.avatar}`, 
                    }
                );
            }
            this.jsonUsuarios[this.editingIndex] = JSON.parse(JSON.stringify(this.usuario))
            this.editModal = false
        },
        listarUsuarios() {
            api.get(`/users`)
            .then(response => {
                this.jsonUsuarios = response.data.data;
                this.totalPaginas = response.data.total_pages;
                this.pagina = response.data.page;
                this.paginaAtual = response.data.page;
                this.porPagina = response.data.per_page;
            })
            .catch(error => console.log('Erro', error.message))
        },
        irParaNovaPagina(pagina) {
            this.paginationUrl=`/users?page=${pagina}`;
            api.get(this.paginationUrl)
                .then((response) => {
                    this.jsonUsuarios = response.data.data;
                })
                .catch(error => console.log('Erro', error.message))
        }
    },
    mounted(){
        this.listarUsuarios();
    }
}
</script>