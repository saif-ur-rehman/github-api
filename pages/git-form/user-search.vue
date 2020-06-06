<template>
<div>
  <v-row>
    <v-col>
      <v-form v-on:submit.prevent="submit" ref="git_form">
        <v-text-field v-model="git_hub_user" :counter="10" label="Name" required></v-text-field>
        <div class="float-right pt-10">
          <v-btn :outlined=true color="warning" class="mr-4" @click="reset">
            Clear Input
          </v-btn>
          <v-btn :outlined=true :loading=sub_loader color="success" class="mr-0" @click="submit">
            Get Repos
          </v-btn>
        </div>
      </v-form>
    </v-col>
    <v-row v-if="git_user_data.owner_info">
      <v-col>
        <v-img :aspect-ratio="1/1" width=auto height=auto :src="git_user_data.owner_info.avatar_url">
          <v-row align="end" class="lightbox white--text pa-2 fill-height">
            <v-col>
              <div class="subheading black--text">{{git_user_data.owner_info.login}}</div>
              <div class="body-1 black--text">{{git_user_data.owner_info.html_url}}</div>
            </v-col>
          </v-row>
        </v-img>
      </v-col>
      <v-col>
        <div class="subheading white--text">{{git_user_data.owner_info.login}}</div>
        <div class="body-1 white--text">{{git_user_data.owner_info.html_url}}</div>
      </v-col>
    </v-row>
  </v-row>
  <v-row v-if="git_user_data.repos_list">
    <v-timeline>
    <v-timeline-item
      v-for="(n,i) in git_user_data.repos_list"
      :key="i"
      large
    >
      <template v-slot:icon>
        <v-avatar>
          <img :src="n.owner.avatar_url">
        </v-avatar>
      </template>
      <template v-slot:opposite>
        <span>
          <v-btn hover=false depressed text class="white--text"
          :href="n.owner.url" target="_blank">{{n.owner.login}}</v-btn>
           Created At {{n.created_at}}
        </span>
      </template>
      <v-card class="elevation-2">
        <v-card-title class="headline"><v-btn hover=false depressed text class="white--text"
          :href="n.html_url" target="_blank">{{n.name}} </v-btn>  ({{n.default_branch}})
          <v-chip v-if='n.language'
                class="ma-2"
                color="info"
                small pill label
                outlined
              >
                <!-- <v-icon v-if='n.language' left>mdi-language-{{n.language.toLowerCase()}}</v-icon> -->
                <v-icon v-if='n.language' left>mdi-language-{{get_icon(n.language.toLowerCase())}}</v-icon>
                {{n.language}}
              </v-chip>
        </v-card-title>
        <v-card-text>{{n.description}}</v-card-text>
        <v-card-text>bvhjsdvjbfe ecjbj kjcbsc</v-card-text>
      </v-card>
    </v-timeline-item>
  </v-timeline>
  </v-row>
</div>
</template>

<script>
export default {
  data() {
    return {
      git_hub_user: 'saif-ur-rehman',
      sub_loader: false,
      git_user_data: {},
    }
  },
  mounted() {
    // this.set_git_user_data()
  },
  computed: {
    // get_icon(language) {
    //   return language;
    // }
  },
  methods: {
    get_icon(language) {
      if(language == 'html') return 'html5'
      if(language == 'css') return 'css3'
      return language;
    },
    reset() {
      this.sub_loader = false;
      this.$refs.git_form.reset();
      this.git_user_data = {};
    },
    submit() {
      this.sub_loader = true;
      let url = "https://api.github.com/users/" + this.git_hub_user + "/repos?type=all"
      var self = this;
      this.$axios.get(url)
        .then((response) => {
          // handle success
          self.sub_loader = false;
          this.set_git_user_data(response);
          //console.log(response);
        })
        .catch((error) => {
          // handle error
          console.log(error);
        })
        .then(() => {
          // always executed
        });
    },
    set_git_user_data(data) {
      console.log(data);

      this.git_user_data.repos_list = data.data;
      data.data.find((el) => {
        console.log(el)
        if(el.owner.login == this.git_hub_user) {
          this.git_user_data.owner_info = el.owner;
        }
      })
    }
  }
}
</script>
