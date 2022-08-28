<template>
  <q-page padding>
    <q-form
      @submit="onSubmit"
      class="row q-col-gutter-sm"
      >
      <q-input
        class="col-lg-6 col-xs-12"
        outlined
        v-model="form.title"
        label="Título"
        lazy-rules
        :rules="[ val => val && val.length > 0 || 'Campo obrigatório' ]"
      />
      <q-input
        class="col-lg-6 col-xs-12"
        outlined
        v-model="form.author"
        label="Autor"
        lazy-rules
        :rules="[ val => val && val.length > 0 || 'Campo obrigatório' ]"
      />

      <div class="col-lg-12 col-xs-12">
        <q-editor
          v-model="form.content"
          min-height="5rem"
        />
      </div>

      <div class="col-12 q-gutter-sm">
        <q-btn
          label="Salvar"
          color="primary"
          class="float-right"
          icon="save"
          type="submit"
        />

        <q-btn
          label="Cancelar"
          color="negative"
          class="float-right"
          text-color="white"
          :to="{ name: 'home' }"
        />
      </div>

    </q-form>
  </q-page>
</template>

<script>

import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter, useRoute } from 'vue-router'

export default defineComponent({
  name: 'FormPost',
  setup () {
    const $q = useQuasar()
    const { post, getById, update } = postsService()
    const router = useRouter()
    const route = useRoute()

    const form = ref({
      title: '',
      content: '',
      author: ''
    })

    onMounted(async () => {
      if (route.params.id) {
        const response = await getById(route.params.id)
        // Alimentando o formulário com o conteúdo que veio no getById para realizar a edição
        form.value = response
      }
    })

    const onSubmit = async () => {
      try {
        if (form.value.id) {
          await update(form.value)
        } else {
          await post(form.value)
        }
        $q.notify({ message: 'Cadastrado com sucesso!', icon: 'check', color: 'positive' })
        router.push({ name: 'home' })
      } catch (error) {
        $q.notify({ message: 'Erro ao cadastrar...!', icon: 'times', color: 'negative' })
      }
    }

    return {
      form,
      onSubmit
    }
  }
})
</script>
