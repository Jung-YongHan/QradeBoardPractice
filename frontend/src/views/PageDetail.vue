<style scoped>
  textarea {
    width: 100%;
  }

  input[type=submit] {
    margin-top: 10px;
  }
</style>

<template>
    <div class="container my-3">
        <!-- 질문 -->
        <h2 class="border-bottom py-2">{{ question.subject }}</h2>
        <div class="card my-3">
            <div class="card-body">
                <div class="card-text" style="white-space: pre-line;">{{ question.content }}</div>
                <div class="d-flex justify-content-end">
                    <div class="badge bg-light text-dark p-2">
                        {{ question.create_date }}
                    </div>
                </div>
            </div>
        </div>
        <!-- 답변 목록 -->
        <h5 class="border-bottom my-3 py-2" v-if="question.answers">
      {{ question.answers.length }}개의 답변이 있습니다.
    </h5>
    <div v-if="question.answers">
      <div v-for="answer in question.answers" :key="answer.id" class="card my-3">
        <div class="card-body">
          <div class="card-text" style="white-space: pre-line;">{{ answer.content }}</div>
          <div class="d-flex justify-content-end">
            <div class="badge bg-light text-dark p-2">
              {{ answer.create_date }}
            </div>
          </div>
        </div>
      </div>
    </div>
        <!-- 답변 등록 -->
        <h4 class="border-bottom py-2">답변 작성하기</h4>
    <ErrorComponent :error="error" />
    <form @submit.prevent="postAnswer" class="my-3">
      <div class="mb-3">
        <textarea rows="10" v-model="content" class="form-control"></textarea>
      </div>
      <input type="submit" value="답변등록" class="btn btn-primary" />
    </form>
    </div>
</template>
<script>
import fastapi from '../lib/api';
import ErrorComponent from "../components/ErrorComponent.vue"

export default {
    components: {
        ErrorComponent
    },
    props: {
        question_id: {
            type: String,
            required: true
        }
    },
    data() {
        return {
            question: {},
            content: "",
            error: { detail: [] },
        };
    },
    methods: {
        getQuestion() {
          const index = this.$route.params.index;
            fastapi('get', `/api/question/list/`, {}, (json) => {
                this.question = json[index];
            });
        },
        postAnswer() {
            let url = `/api/answer/create/${this.question_id}`
            let params = {
                content: this.content
            }
            fastapi('post', url, params, () => {
                    this.content = ""
                    this.error = { detail: [] }
                    this.getQuestion()
                },
                (err_json) => {
                    this.error = err_json
                }
            )
        },
    },
    created() {
        this.getQuestion();
    }
}
</script>