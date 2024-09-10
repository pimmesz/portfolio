<template>
  <v-container class="portfolio">
    <v-row class="mb-12">
      <v-col cols="12" sm="8" offset-sm="2">
        <v-card variant="text" class="text-center">
          <v-avatar size="150" class="mb-4">
            <v-img src="pimavatar.jpeg" alt="Pim Zonneveld"></v-img>
          </v-avatar>
          <v-card-title class="text-h2 font-weight-bold pb-0 mb-0 text-center">Pim Zonneveld</v-card-title>
          <v-card-subtitle class="text-h4 text-center">Full Stack Developer</v-card-subtitle>
          <v-card-text>
            <p class="text-body-1 mb-4 text-center">I'm a passionate developer with expertise in Vue.js, Node.js, and Python. I love creating elegant solutions to complex problems and continuously learning new technologies.</p>
            <v-chip-group class="justify-center">
              <v-chip v-for="skill in skills" :key="skill" color="secondary" class="ma-2 skill-chip" label>{{ skill }}</v-chip>
            </v-chip-group>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>

    <v-row class="mb-12">
      <v-col cols="12">
        <v-card variant="text">
          <v-card-title class="text-h3 mb-4 text-center"><strong>Projects</strong></v-card-title>
          <v-row>
            <v-col v-for="project in projects" :key="project.id" cols="12" sm="6" md="4">
              <v-card class="bg-transparent-secondary h-100 project-card d-flex flex-column" elevation="2" rounded="lg">
                <v-img :src="project.image" :alt="project.title" height="200" cover></v-img>
                <v-card-title class="text-h5 font-weight-bold text-white">{{ project.title }}</v-card-title>
                <v-card-text class="text-body-2 text-white flex-grow-1">{{ project.description }}</v-card-text>
                <v-card-actions class="mt-auto">
                  <v-row>
                    <v-col cols="6">
                      <v-btn
                        :href="project.link"
                        target="_blank"
                        color="white"
                        rounded="pill"
                        elevation="2"
                        block
                      >
                        <v-icon left class="mr-2">mdi-open-in-new</v-icon>
                        Site
                      </v-btn>
                    </v-col>
                    <v-col cols="6">
                      <v-btn
                        :href="project.github"
                        target="_blank"
                        color="white"
                        rounded="pill"
                        elevation="2"
                        block
                      >
                        <v-icon left class="mr-2">mdi-github</v-icon>
                        GitHub
                      </v-btn>
                    </v-col>
                  </v-row>
                </v-card-actions>
              </v-card>
            </v-col>
          </v-row>
        </v-card>
      </v-col>
    </v-row>

    <v-row class="mb-12">
      <v-col cols="12" sm="8" offset-sm="2">
        <v-card variant="text">
          <v-card-title class="text-h3 mb-4 text-center"><strong>Get in Touch</strong></v-card-title>
          <v-form @submit.prevent="submitForm">
            <v-text-field v-model="contactForm.name" label="Your Name" required outlined rounded="lg" class="mb-4"></v-text-field>
            <v-text-field 
              v-model="contactForm.email" 
              label="Your Email" 
              type="email" 
              required 
              outlined 
              rounded="lg" 
              class="mb-4 white-label"
              :rules="[v => !!v || 'E-mail is required', v => /.+@.+\..+/.test(v) || 'E-mail must be valid']"
            ></v-text-field>
            <v-textarea v-model="contactForm.message" label="Your Message" required outlined rounded="lg" class="mb-4"></v-textarea>
            <v-btn type="submit" color="white" x-large block rounded="pill" elevation="2">Send Message</v-btn>
          </v-form>
        </v-card>
      </v-col>
    </v-row>

    <v-footer class="mt-12 text-center text-white">
      <v-row>
        <v-col cols="12">
          <v-btn v-for="icon in socialIcons" :key="icon" :href="getSocialLink(icon)" target="_blank" icon class="mx-2">
            <v-icon size="24">{{ icon }}</v-icon>
          </v-btn>
        </v-col>
        <v-col cols="12">
          <p class="mb-0">&copy; {{ currentYear }} Pim Zonneveld. All rights reserved.</p>
        </v-col>
      </v-row>
    </v-footer>
  </v-container>
</template>

<script lang="ts">
import { defineComponent, ref, reactive, computed } from 'vue';
import axios from 'axios';

export default defineComponent({
  name: 'HomeView',
  setup() {
    const skills = ref(['Vue.js', 'Node.js', 'Python', 'TypeScript', 'RESTful APIs', 'Git']);
    const projects = ref([
      {
        id: 1,
        title: 'Tube.yt',
        description: 'An fullstack automated YouTube music curation platform built with Vue.js and Node.js, featuring automatic video aggregation from popular music channels.',
        image: '/tubeytdemo.gif',
        link: 'https://tube.yt/',
        github: 'https://github.com/pimmesz/boilertube'
      },
      // {
      //   id: 2,
      //   title: 'Weather App',
      //   description: 'A responsive weather application using Vue.js and OpenWeatherMap API, with location-based forecasts and interactive maps.',
      //   image: 'https://via.placeholder.com/300x200',
      //   github: '#'
      // },
      // {
      //   id: 3,
      //   title: 'Task Manager',
      //   description: 'A productivity app built with Vue.js and Firebase for real-time updates, featuring collaborative task lists and progress tracking.',
      //   image: 'https://via.placeholder.com/300x200',
      //   github: '#'
      // }
    ]);

    const contactForm = reactive({
      name: '',
      email: '',
      message: ''
    });

    const socialIcons = ref(['mdi-github', 'mdi-linkedin', 'mdi-twitter']);

    const currentYear = computed(() => new Date().getFullYear());

    const submitForm = async () => {
      console.log('Form submitted:', contactForm);
      
      const telegramApiKey = import.meta.env.VITE_TELEGRAM_API_KEY;
      const telegramChatId = import.meta.env.VITE_TELEGRAM_CHAT_ID;
      
      const message = `New contact form submission:
        Name: ${contactForm.name}
        Email: ${contactForm.email}
        Message: ${contactForm.message}`;

      try {
        await axios.post(`https://api.telegram.org/bot${telegramApiKey}/sendMessage`, {
          chat_id: telegramChatId,
          text: message
        });
        console.log('Telegram message sent successfully');
      } catch (error) {
        console.error('Error sending Telegram message:', error);
      }

      Object.keys(contactForm).forEach(key => contactForm[key as keyof typeof contactForm] = '');
    };

    const getSocialLink = (icon: string): string => {
      const links: Record<string, string> = {
        'mdi-github': 'https://github.com/pimmesz',
        'mdi-linkedin': 'https://www.linkedin.com/in/pimzonneveld/',
        'mdi-twitter': 'https://x.com/pim_gg_'
      };
      return links[icon] || '#';
    };

    return {
      skills,
      projects,
      contactForm,
      socialIcons,
      currentYear,
      submitForm,
      getSocialLink
    };
  }
});
</script>

<style scoped>
.bg-transparent {
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.1), rgba(255, 255, 255, 0.2)) !important;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.18);
}

.bg-transparent-secondary {
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.05), rgba(255, 255, 255, 0.1)) !important;
  backdrop-filter: blur(5px);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.portfolio {
  color: #ffffff;
}

.v-card {
  transition: all 0.3s ease;
}

.v-btn {
  text-transform: none;
  font-weight: 600;
}

.skill-chip {
  opacity: 0.8;
  color: #ffffff !important;
  font-weight: 500;
}

footer {
  background: none !important;
}

.white-label ::v-deep .v-label {
  color: white !important;
}

.white-label ::v-deep .v-messages {
  color: white !important;
}
</style>
