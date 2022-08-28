<template>
  <q-page padding>
    <q-table
      title="Artigos"
      :rows="posts"
      :columns="columns"
      row-key="name"
    >
    <template v-slot:top>
        <span class="text-h5">Artigos</span>
        <q-space />
        <q-btn color="primary" label="Novo" :to="{ name: 'formPost' }" />
      </template>
      <template v-slot:body-cell-actions="props">
        <q-td :props="props" class="q-gutter-sm">
          <q-btn icon="edit" color="info" dense size="sm" @click="editPost(props.row.id)"/>
          <q-btn icon="delete" color="negative" dense size="sm" @click="deletePost(props.row.id)"/>
        </q-td>
      </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
// Importando a API através de um service exclusivo
import postsService from 'src/services/posts'
// Importando Plugin de notificações
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref([])
    const { list, remove } = postsService()
    const $q = useQuasar()
    const router = useRouter()

    const columns = [
      { name: 'id', align: 'left', label: 'ID', field: 'id', sortable: true },
      { name: 'title', align: 'center', label: 'Título', field: 'title', sortable: true },
      { name: 'author', align: 'center', label: 'Autor', field: 'author', sortable: true },
      { name: 'actions', align: 'right', label: 'Ações', field: 'actions' }
    ]

    onMounted(() => {
      getPosts()
    })

    const getPosts = async () => {
      try {
        const data = await list()
        posts.value = data
      } catch (error) {
        $q.notify({ message: 'Erro ao obter postagens...!', icon: 'times', color: 'negative' })
      }
    }

    const deletePost = async (id) => {
      try {
        $q.dialog({
          title: 'Excluir',
          message: 'Deseja mesmo remover este Post?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(id)
          $q.notify({ message: 'Excluído com sucesso!', icon: 'check', color: 'positive' })
          await getPosts()
        })
      } catch (error) {
        $q.notify({ message: 'Erro ao excluir...!', icon: 'times', color: 'negative' })
      }
    }

    const editPost = async (id) => {
      router.push({ name: 'formPost', params: { id } })
    }

    return {
      posts,
      columns,
      deletePost,
      editPost
    }
  }
})
</script>
