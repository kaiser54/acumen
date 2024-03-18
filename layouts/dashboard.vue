<template>
  <div class="layout__wrapper">
    <aside>
      <div class="logo__comp flex">
        <Logo />
        <div class="circle flex" v-html="u_compress_v"></div>
      </div>

      <p class="snippet base__font">App Name</p>

      <ul class="flex">
        <li v-for="link in navLinks" :key="link.label">
          <NuxtLink :to="link.url" class="nav__links flex">
            <span v-html="link.svg"></span>
            <span>{{ link.label }}</span>
          </NuxtLink>
        </li>
      </ul>

      <div class="hr"></div>

      <div class="nav__links flex base__font footer">
        <span v-html="u_left_arrow"></span>
        <span>Collapse</span>
      </div>
      <div class="nav__links flex base__font error feedback footer">
        <span v-html="u_comment_exclamaion"></span>
        <span>Give Feedback</span>
      </div>
    </aside>

    <div class="content__wrap">
      <div class="nav__header flex">
        <p class="user__name base__font">Hello Ayomide! 👋🏼</p>
        <div class="cta flex">
          <button class="base__font btn dwnld">Download Report</button>
          <button
            class="avatar circle flex base__font btn"
            style="--width: 40px; --height: 40px; --bg: #efefef"
          >
            AO
          </button>
          <svg
            width="7"
            height="4"
            viewBox="0 0 7 4"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M6 1L3.5 3L1 1"
              stroke="#0068FF"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </div>
      </div>

      <div class="hr"></div>
      <slot />
    </div>
  </div>
</template>

<script setup>
import {
  u_compress_v,
  u_circuit,
  u_analytics,
  u_comment_dot,
  u_silence,
  light_bulb,
  fi_bell,
  u_setting,
  u_left_arrow,
  u_comment_exclamaion,
} from "~/utils/svg.js";

const navLinks = [
  {
    label: "Get Started",
    svg: u_circuit,
    url: "/",
  },
  {
    label: "Analytics",
    svg: u_analytics,
    url: "/analytics",
  },
  {
    label: "Chat",
    svg: u_comment_dot,
    url: "chat",
  },
  {
    label: "Moderation",
    svg: u_silence,
    url: "/",
  },
  {
    label: "API & Auth Keys",
    svg: light_bulb,
    url: "/",
  },
  {
    label: "Announcement",
    svg: fi_bell,
    url: "/",
  },
  {
    label: "Settings",
    svg: u_setting,
    url: "/",
  },
];
</script>

<style>
.layout__wrapper {
  background-color: #f3f7ff;
}
.content__wrap {
  min-height: 100vh;
  max-width: calc(100% - 270px);
  width: 100%;
  margin-left: auto;
}

.nav__header {
  align-items: center;
  justify-content: space-between;
  padding: 22px 49px 30px 52px;
}

.user__name {
  font-size: 16px;
  font-weight: 400;
  line-height: 24px;
  color: #9999bc;
}

.cta {
  align-items: center;
  gap: 12px;
}

button.dwnld {
  border-radius: 30px;
  padding: 4px 35px 4px 35px;
  color: #fff;
  font-weight: bold;
  background-color: #4568d1;
}

aside {
  position: fixed;
  height: 100vh;
  width: 270px;
  overflow-y: auto;
  overflow-x: hidden;
  background-color: #fff;
  z-index: 9;
}

aside::-webkit-scrollbar {
  width: 3px !important;
}

aside::-webkit-scrollbar-track {
  background: #f5f7fc; /* Set background color of the scrollbar track */
}

aside::-webkit-scrollbar-thumb {
  background: #9999bc; /* Set background color of the scrollbar thumb */
  border-radius: 20px; /* Set border radius of the scrollbar thumb */
}

.logo__comp {
  gap: 39px;
  padding: 37px 16px 9px 50px;
}

.snippet {
  color: #8d9091;
  font-size: 12px;
  line-height: 24px;
  padding-left: 50px;
}

ul {
  flex-direction: column;
  align-items: flex-start;
  gap: 34px;
  margin-top: 42px;
  margin-left: 46px;
  list-style: none;
}

li {
  width: 100%;
}

.nav__links {
  align-items: center;
  gap: 16px;
  padding: 16px;
  width: 100%;
  height: 24px;
  font-size: 16px;
  font-weight: 300;
  letter-spacing: 0.3px;
  line-height: 24px;
  color: #9999bc;
  transition: all 0.3s ease-in;
}

.nav__links:hover {
  color: #ea8d51;
}

aside .hr {
  margin: 51px 29px 25px 29px;
}

.error {
  color: #d53120;
}

.feedback {
  margin-block: 130px;
}

.footer {
  margin-left: 46px;
}
</style>

<style>
.nav__links svg path {
  transition: all 0.3s ease-in;
}
.nav__links:hover span svg path {
  fill: #ea8d51;
}
.nav__links:hover span svg.bulb path {
  stroke: #ea8d51;
  fill: none;
}

.error:hover span,
.error:hover span svg path {
  fill: #d53120;
  color: #d53120;
}
a.router-link-active.router-link-exact-active.nav__links.flex {
  color: #ea8d51;
}

a.router-link-active.router-link-exact-active.nav__links.flex span svg path {
  fill: #ea8d51;
}
a.router-link-active.router-link-exact-active.nav__links.flex span svg.bulb path {
  stroke: #ea8d51;
  fill: none;
}
</style>
