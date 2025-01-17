<template>
  <div>
    <header class="flex flex-col-reverse py-24 md:(items-center flex-row)">
      <div class="md:w-8/12">
        <div class="space-y-px">
          <div
            class="text-2xl font-semibold text-gray-900 md:text-3xl md:text-4xl dark:text-gray-100"
          >
            <h1>Self taught</h1>
            <h1><span class="text-blue-600">Full-stack</span> web developer</h1>
          </div>

          <p class="text-gray-800 dark:text-gray-200">
            Hi there, my name is Abdulbaki, I am from Turkey and I am a self
            taught web developer. I recently started building complex web apps
            using
            <SmartLink href="https://vuejs.org/" class="description-link" blank>
              Vue.js</SmartLink
            >,
            <SmartLink
              href="https://nuxtjs.org/"
              class="description-link"
              blank
            >
              Nuxt.js</SmartLink
            >
            and
            <SmartLink
              href="https://windicss.org/"
              class="description-link"
              blank
            >
              Windi CSS</SmartLink
            >.
          </p>
        </div>

        <Status class="mt-2" />
      </div>

      <div class="flex flex-shrink-0 mb-8 md:(justify-end mb-0 w-4/12)">
        <SmartImage
          src="/assets/images/irl_image.webp"
          class="rounded-full h-40 ring-black ring-4 ring-opacity-5 w-40 dark:(ring-white ring-opacity-5)"
        />
      </div>
    </header>

    <section id="projects">
      <h2 class="mt-10 text-2xl font-semibold text-gray-900 dark:text-gray-100">
        Projects I currently work on
      </h2>

      <div class="mt-2 grid gap-2 md:(gap-4 grid-cols-3)">
        <div
          v-for="(project, index) in getProjects.featured"
          :key="`project-featured-${index}`"
        >
          <SmartLink
            v-if="project.to || project.href"
            :href="project.to || project.href"
            title="Click to visit this project"
            :blank="!!project.href"
          >
            <CardProject
              :title="project.title"
              :description="project.description"
              :image="project.image"
              class="h-full"
            />
          </SmartLink>

          <CardProject
            v-else
            :title="project.title"
            :description="project.description"
            :image="project.image"
            class="h-full"
          />
        </div>
      </div>

      <div class="mt-2 grid gap-2 md:(mt-4 gap-4 grid-cols-2)">
        <SmartLink
          v-for="(project, index) in getProjects.rest"
          :key="`project-rest-${index}`"
          :href="project.to"
        >
          <CardProject
            :title="project.title"
            :description="project.description"
            class="h-full"
          />
        </SmartLink>
      </div>
    </section>

    <section
      id="experiences"
      class="mt-4 grid gap-6 sm:mt-6 md:(md:mt-10 gap-8 grid-cols-2)"
    >
      <div>
        <h3 class="text-xl font-semibold text-gray-900 dark:text-gray-100">
          Experience
        </h3>

        <div class="grid gap-2 mt-4">
          <CardExperience
            v-for="(experience, index) in experiences.jobs"
            :key="`experience-job-${index}`"
            :title="experience.title"
            :url="experience.url"
            :date="experience.date"
            :position="experience.position"
          />
        </div>
      </div>

      <div>
        <h3 class="text-xl font-semibold text-gray-900 dark:text-gray-100">
          Education
        </h3>

        <div class="grid gap-2 mt-4">
          <CardExperience
            v-for="(experience, index) in experiences.education"
            :key="`experience-education-${index}`"
            :title="experience.title"
            :url="experience.url"
            :date="experience.date"
            :position="experience.position"
          />
        </div>
      </div>
    </section>

    <section id="technologies" class="mt-6">
      <h3
        class="mt-4 text-xl font-semibold text-gray-900 md:mt-10 dark:text-gray-100"
      >
        Technologies I use
      </h3>

      <div class="grid grid-cols-2 gap-2 mt-4 sm:grid-cols-3 md:grid-cols-4">
        <CardSkill
          v-for="(skill, index) in skills"
          :key="`skill-${index}`"
          :title="skill"
        />
      </div>
    </section>

    <section id="repositories" class="mt-6">
      <h2 class="mt-10 text-xl font-semibold text-gray-900 dark:text-gray-100">
        My GitHub repositories
      </h2>

      <div class="mt-4">
        <div
          v-if="$fetchState.pending"
          class="grid grid-cols-1 gap-2 md:grid-cols-2"
        >
          <SkeletonLoader
            v-for="item in 8"
            :key="`repo-skeleton-${item}`"
            type="repository"
          />
        </div>

        <div
          v-else-if="$fetchState.error"
          class="text-gray-900 dark:text-gray-100"
        >
          Couldn't load GitHub repositories.
        </div>

        <div
          v-else-if="repos.length > 0"
          class="grid grid-cols-1 gap-2 md:grid-cols-2"
        >
          <SmartLink
            v-for="(repo, index) in repos"
            :key="`repo-${index}`"
            :href="repo.html_url"
            title="Click here to visit this repository"
            blank
          >
            <CardRepository
              :name="repo.name"
              :language="repo.language"
              :stars="repo.stargazers_count"
              :description="repo.description"
              class="h-full"
            />
          </SmartLink>
        </div>
      </div>
    </section>

    <section id="socials" class="mt-6">
      <h2 class="mt-10 text-xl font-semibold text-gray-900 dark:text-gray-100">
        Follow me
      </h2>

      <Socials class="mt-2" />
    </section>
  </div>
</template>

<script lang="ts">
import Vue from "vue"

/* Interfaces */
import type { Repository } from "../types/Response/GitHub"

interface Project {
  title: string
  description: string
  href?: string
  to?: string
  image?: string
}

interface Experience {
  title: string
  url: string
  position: string
  date: string
}

interface ExperienceObject {
  jobs: Experience[]
  education: Experience[]
}

export default Vue.extend({
  data() {
    return {
      repos: [] as Repository[],
      projects: [
        {
          title: "Discord Templates",
          description:
            "Create your own kingdom with our diverse range of Discord templates!",
          image: "https://i.vgy.me/jbf1UB.jpg",
          href: "https://discords.com/templates",
        },
        {
          title: "PreMiD",
          description:
            "PreMiD is a simple, configurable utility that allows you to show what you're doing on the web in your Discord now playing status.",
          image: "https://i.vgy.me/rhHgcJ.jpg",
          to: "/projects/premid",
        },
        {
          title: "This Website",
          description:
            "The website where you can learn more and more about me!",
          image: "https://i.vgy.me/sAQScm.jpg",
          to: "/",
        },
        {
          title: "Unblock Please",
          description:
            "Unblock Imgur and Pastebin access using secure proxies!",
          to: "/projects/unblock-please",
        },
        {
          title: "is-inside.me",
          description: "Free image and file uploading service for ShareX!",
          to: "/projects/is-inside-me",
        },
      ] as Project[],
      experiences: {
        jobs: [
          {
            title: "IZM Game Studios",
            url: "https://izmgamestudios.com",
            position: "Solution Partner",
            date: "2020-",
          },
          {
            title: "TruckersMP",
            url: "https://truckersmp.com/user/1648033",
            position: "Communitiy Moderator & Translator",
            date: "2020-",
          },
          {
            title: "WA Web+ Extension",
            url: "https://wawplus.com",
            position: "Translator",
            date: "2020-2021"
          },
          {
            title: "Batakköylü Düğün Salonu",
            url: "https://batakkoylu.netlify.app",
            position: "Web Developer",
            date: "2020-2020",
          },
          {
            title: "EGGSY's Discord Bot",
            url: "https://top.gg/bot/eggsy",
            position: "Creator, Developer and Maintainer",
            date: "2017-2019",
          },
          {
            title: "Discord Bot List",
            url: "https://top.gg/user/162969778699501569",
            position: "Website Moderator",
            date: "2017-2018",
          },
        ],
        education: [
          {
            title: "Muş Alparslan University",
            url: "http://alparslan.edu.tr/",
            position: "English Language Teaching",
            date: "2020-",
          },
          {
            title: "Rotosis Robotics",
            url: "https://www.rotosis.com/",
            position: "Intern",
            date: "2017-2018",
          },
          {
            title: "Adil Karaağaç MTAL",
            url: "https://akatl.meb.k12.tr/",
            position: "Automation Systems",
            date: "2014-2018",
          },
        ],
      } as ExperienceObject,
      skills: [
        "JavaScript",
        "HTML5",
        "Nuxt.js",
        "Vue.js",
        "Tailwind CSS",
        "Windi CSS",
        "Node.js",
        "TypeScript",
        "Sass",
        "Figma",
        "Firebase",
        "WordPress",
        "PHP",
      ],
    }
  },
  fetchOnServer: false,
  async fetch() {
    const filter = ["eggsy", "DBM", "eggsywashere.github.io"]

    const repos: Repository[] = (
      await this.$axios.get(
        "https://api.github.com/users/eggsy/repos?per_page=100"
      )
    ).data

    this.repos = repos
      ?.filter((repo) => repo.fork === false && !filter.includes(repo.name))
      ?.sort((a, b) => b?.stargazers_count - a?.stargazers_count)
  },
  head: {
    title: "Home",
  },
  computed: {
    /**
     * Slices the first three projects and creates an object with them, and the rest.
     * @returns {{featured: Project[], rest: Project[]}} The projects object.
     */
    getProjects(): { featured: Project[]; rest: Project[] } {
      const projects = this.projects

      return {
        featured: projects?.slice(0, 3) || [],
        rest: projects?.slice(3) || [],
      }
    },
  },
})
</script>

<style lang="scss" scoped>
.description-link {
  @apply border-gray-500 border-b-2 border-opacity-50 hover:border-opacity-75;
}
</style>
