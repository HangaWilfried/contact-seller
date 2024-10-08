<template>
  <header class="flex justify-between items-center">
    <h1>Fatre</h1>
    <h2 v-if="session.token.isLoggedIn">
      welcome
      <span class="text-rose-500">
        {{ JSON.stringify(session.token.decode) }}
      </span>
    </h2>
    <div class="flex gap-2 items-center" v-else>
      <RouterLink :to="{ name: 'loginPage' }">
        <ButtonWrapper :label="t('login')" />
      </RouterLink>
      <RouterLink :to="{ name: 'signupPage' }">
        <ButtonWrapper :label="t('signup')" />
      </RouterLink>
    </div>
  </header>
  <section class="space-y-4">
    <div>list of publications</div>
    <div v-if="isLoading" class="cards">
      <CardSkeleton />
    </div>
    <div class="cards" v-else-if="posts.length">
      <RouterLink
        :to="{ name: 'productDetailsPage', params: { id: post.id } }"
        v-for="post in posts"
        :key="post.id"
      >
        <PostCard :post="post" />
      </RouterLink>
    </div>
    <div v-else>
      <EmptySVG />
    </div>
  </section>
  <footer></footer>
</template>

<script setup lang="ts">
import type { Post } from "@/domain/post";
import { usePostStore } from "@/stores/post";
import { useTranslation } from "@/utils/i18n";
import { RouterLink } from "vue-router";
import { useSessionStore } from "@/stores/session";
import { onBeforeMount, defineAsyncComponent, ref, type Ref } from "vue";

import ButtonWrapper from "@/components/ButtonWrapper.vue";

const PostCard = defineAsyncComponent(
  () => import("@/components/PostCard.vue"),
);
const CardSkeleton = defineAsyncComponent(
  () => import("@/components/CardSkeleton.vue"),
);
const EmptySVG = defineAsyncComponent(() => import("@/icons/EmptySVG.vue"));

const t = useTranslation();

const store = usePostStore();
const session = useSessionStore();

const posts = ref<Post[]>([]) as Ref<Post[]>;
const isLoading = ref<boolean>(false);

onBeforeMount(async () => {
  isLoading.value = true;
  const { data } = await store.getMyPosts();
  posts.value = data!;
  isLoading.value = false;
});
</script>
