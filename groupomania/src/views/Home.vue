<template>
  <div>
    <Navbar />
    <main class="px-5 py-3 bg-gray-100 mt-10">
      <section>
        <h1
          class="
            text-2xl
            tracking-wider
            font-semibold
            text-gray-700 text-center
          "
        >
          Bienvenue, {{ user.name }}
        </h1>
        <div
          v-for="(post, index) in posts"
          :key="index"
          class="mx-auto px-4 py-8 max-w-xl my-10"
        >
          <div class="bg-white shadow-2xl rounded-lg mb-6 tracking-wide">
            <i
              v-if="post.userId == user.userId || user.isAdmin == 1"
              class="fas fa-times"
              @click="deletePost(post.postId)"
            ></i>
            <div class="md:flex-shrink-0">
              <img
                v-if="post.postImage"
                :src="post.postImage"
                alt="image du post"
                class="
                  w-full
                  h-64
                  rounded-lg rounded-b-none
                  md:object-scale-down
                "
              />
            </div>
            <div class="px-4 py-2 mt-2">
              <h2
                class="font-bold text-2xl text-gray-800 tracking-normal"
                v-if="post.postContent"
              >
                {{ post.postContent }}
              </h2>
              <div class="author flex items-center -ml-3 my-3">
                <div class="user-logo">
                  <img
                    v-if="post.userPicture !== null"
                    :src="post.userPicture"
                    alt="Photo de profil"
                    class="
                      h-8
                      w-8
                      object-cover
                      rounded-full
                      box-content
                      border-2 border-gray-400
                      mr-1
                      ml-1
                    "
                  />
                  <i
                    v-else
                    class="fas fa-2x fa-user-circle text-gray-600 mr-3 ml-3"
                  ></i>
                </div>
                <h2 class="text-sm tracking-tighter text-gray-900">
                  <a href="#">Par {{ post.userName }} </a>
                  <span class="text-gray-600">
                    Publié {{ moment(post.postDate).calendar() }}</span
                  >
                </h2>
              </div>
              <div class="flex justify-between px-10 py-6">
                <a @click="Like(post.postId, 1)">
                  <i class="fas fa-thumbs-up fa-lg h-5 w-6"></i
                ></a>
                <p>Like {{ post.likes.LikesNumber }}</p>
                <a @click="Like(post.postId, -1)"
                  ><i class="fas fa-thumbs-down fa-lg h-5 w-6"></i
                ></a>
                <a @click="showComment = !showComment"
                  ><i class="fas fa-comment fa-lg h-5 w-6"></i
                ></a>
              </div>
            </div>
            <div
              v-show="showComment"
              v-for="(comment, i) in post.comments"
              :key="i"
            >
              <div
                id="messages"
                class="
                  flex flex-col
                  space-y-4
                  p-3
                  overflow-y-auto
                  scrollbar-thumb-blue
                  scrollbar-thumb-rounded
                  scrollbar-track-blue-lighter
                  scrollbar-w-2
                  scrolling-touch
                "
              >
                <div class="chat-message">
                  <div class="flex items-end">
                    <div
                      class="
                        flex flex-col
                        space-y-2
                        text-xs
                        max-w-xs
                        mx-2
                        order-2
                        items-start
                      "
                    >
                      <div>
                        <p class="px-4 py-2 inline-block font-bold">
                          {{ comment.userName }}
                        </p>
                        <span
                          class="
                            px-4
                            py-2
                            rounded-lg
                            inline-block
                            rounded-bl-none
                            bg-gray-300
                            text-gray-600
                          "
                        >
                          {{ comment.commentContent }}
                          <i
                            v-if="
                              comment.userId == user.userId || user.isAdmin == 1
                            "
                            class="fas fa-times ml-5"
                            @click="deleteComment(comment.commentId)"
                          ></i>
                        </span>
                      </div>
                    </div>
                    <img
                      v-if="comment.userPicture !== null"
                      :src="comment.userPicture"
                      alt="Photo de profil"
                      class="
                        h-8
                        w-8
                        object-cover
                        rounded-full
                        box-content
                        border-2 border-gray-400
                      "
                    />
                    <i
                      v-else
                      class="fas fa-2x fa-user-circle text-gray-600"
                    ></i>
                  </div>
                </div>
              </div>
            </div>
            <div
              class="
                flex
                mx-auto
                items-center
                justify-center
                mt-30
                mx-8
                mb-4
                max-w-lg
              "
            >
              <form
                @submit.prevent="addComment(post.postId)"
                class="w-full max-w-xl bg-white rounded-lg px-4 pt-2"
              >
                <div class="flex flex-wrap -mx-3 mb-6">
                  <h2 class="px-4 pt-3 pb-2 text-gray-800 text-lg">
                    Ajouter un commentaire
                  </h2>
                  <div class="w-full md:w-full px-3 mb-2 mt-2">
                    <textarea
                      class="
                        bg-gray-100
                        rounded
                        border border-gray-400
                        leading-normal
                        resize-none
                        w-full
                        h-20
                        py-2
                        px-3
                        font-medium
                        placeholder-gray-700
                        focus:outline-none focus:bg-white
                      "
                      name="body"
                      v-model="comment"
                      placeholder="Ecrivez votre commentaire"
                      required
                    ></textarea>
                  </div>
                  <div class="w-full md:w-full flex items-end md:w-full px-3">
                    <div class="-mr-1">
                      <input
                        type="submit"
                        class="
                          bg-white
                          text-gray-700
                          font-medium
                          py-1
                          px-4
                          border border-gray-400
                          rounded-lg
                          tracking-wide
                          mr-1
                          hover:bg-gray-100
                        "
                        value="Post Comment"
                      />
                    </div>
                  </div>
                </div>
              </form>
            </div>
          </div>
        </div>
      </section>
    </main>
  </div>
</template>

<script>
import { ref } from "vue";
import moment from "moment";
import Navbar from "../components/Navbar.vue";
moment.locale("fr");
export default {
  data() {
    return {
      user: [],
      showUserMenu: false,
      showSideBar: false,
      showSearch: false,
      showComment: false,
      posts: ref([]),
      search: ref(""),
      postId: "",
      comment: "",
      data: []
    };
  },
  components: {
    Navbar,
  },
  methods: {
    moment,
    getCurrentUser() {
      const option = {
        method: "GET",
        headers: { "Content-Type": "application/json" },
        credentials: "include",
      };
      fetch("http://localhost:3000/api/user/currentuser", option)
        .then((response) => response.json())
        .catch((error) => {
          console.error("There was an error!", error);
        })
        .then((data) => {
          this.user = data;
        });
    },
    getAllPost() {
      const option = {
        method: "GET",
        headers: { "Content-Type": "application/json" },
        credentials: "include",
      };
      fetch("http://localhost:3000/api/post", option)
        .then((response) => response.json())
        .catch((error) => {
          console.error("There was an error!", error);
        })
        .then((data) => {
          this.posts = data.posts;

        });
    },
    Like(postId, like) {
      const option = {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ postId, rate: like }),
        credentials: "include",
      };
      fetch("http://localhost:3000/api/like", option)
        .then((response) => response.json())
        .catch((error) => {
          console.error("There was an error!", error);
        })
        .then((data) => {
          this.data = data
          this.getAllPost();

        });
    },
    addComment(postId) {
      const option = {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ postId, content: this.comment }),
        credentials: "include",
      };
      fetch("http://localhost:3000/api/comment", option)
        .then((response) => response.json())
        .catch((error) => {
          console.error("There was an error!", error);
        })
        .then((data) => {
          this.data = data
          this.comment = "";
          this.getAllPost();
          this.comment = "";
        });
    },
    deleteComment(commentId) {
      const option = {
        method: "DELETE",
        credentials: "include",
      };
      fetch(`http://localhost:3000/api/comment/${commentId}`, option)
        .then((response) => response.json())
        .catch((error) => {
          console.error("There was an error!", error);
        })
        .then((data) => {
          this.data = data
          this.getAllPost();
        });
    },
    deletePost(postId) {
      const option = {
        method: "DELETE",
        credentials: "include",
      };
      fetch(`http://localhost:3000/api/post/${postId}`, option)
        .then((response) => response.json())
        .catch((error) => {
          console.error("There was an error!", error);
        })
        .then((data) => {
          this.data = data
          this.getAllPost();

        });
    },
  },
  beforeMount() {
    this.getCurrentUser();
    this.getAllPost();
  },
};
</script>

<style>
body {
  background-color: #f3f4f6;
}
</style>
