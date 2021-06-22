<template>
    <q-page padding>
        <q-form v-on:keyup.enter="logar()" :model="formularioLogin" ref="formularioLogin" class="q-gutter-md">
            <q-input filled lazy-rules type="email" v-model="formularioLogin.email" label="Email" 
                :rules="[ val => val && val.length > 0 || 'Insira o email']">
            </q-input>
            <q-input filled type="password" lazy-rules v-model="formularioLogin.senha"
                :rules="[ val => val && val.length > 0 || 'Insira a senha']" label="Senha">
            </q-input>
            <div class="row justify-center content-center">
                <q-btn label="Logar" @click="logar()" color="primary"></q-btn>
                <q-btn label="Limpar" @click="limpar()" color="primary" flat class="q-ml-sm"></q-btn>
            </div>
        </q-form>
    </q-page>
</template>
<script>
import { api } from 'src/boot/axios'
import { useQuasar } from 'quasar'
export default {
    setup () {
        const $q = useQuasar()

        return {
            exibirNotificacao($tipo, $mensagem, $legenda) {
                $q.notify({
                message: $mensagem,
                caption: $legenda,
                type: $tipo,
                })
            }
        }
    },
    data () {
        return {
            formularioLogin: {}
        }
    },
    methods: {
        async logar () {
            const resposta = await api.post(`/login`, 
                {
                    email: `${this.formularioLogin.email}`, 
                    password: `${this.formularioLogin.senha}`
                }
            ).then((result) => {
                this.$router.push({ path: 'usuarios' });
            }).catch((error) => {
                this.exibirNotificacao('negative','Usuário ou senha incorretos', 'Por favor, tente novamente!');
            });
        },
        limpar () {
            this.formularioLogin = {}
        }
    }
}
</script>