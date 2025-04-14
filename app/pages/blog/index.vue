<script setup lang="ts">
const route = useRoute()

const { data: page } = await useAsyncData('blog', () => queryCollection('blog').first())

const { data } = await useAsyncData(route.path, () => {
  return Promise.all([
    queryCollection('posts').order('date', 'DESC').all(),
    queryCollection('posts').count()
  ])
}, {
  transform: data => {
    return {
      posts: data[0],
      count: data[1]
    }
  }
})

useSeoMeta({
  title: page.value?.title,
  ogTitle: page.value?.title,
  description: page.value?.description,
  ogDescription: page.value?.description
})

defineOgImageComponent('Saas')
</script>

<template>
  <UContainer>
    <UPageHeader
      v-bind="page"
      class="py-[50px]"
      :headline="`${page?.headline ? page.headline + ' | ' : ''} ${data?.count} ${data?.count === 1 ? 'post' : 'posts'}`"
    />

    <UPageBody>
      <UBlogPosts>
        <UBlogPost
          v-for="(post, index) in data?.posts"
          :key="index"
          :to="post.path"
          :title="post.title"
          :description="post.description"
          :image="post.image"
          :date="new Date(post.date).toLocaleDateString('en', { year: 'numeric', month: 'short', day: 'numeric' })"
          :authors="post.authors"
          :badge="post.badge"
          :orientation="index === 0 ? 'horizontal' : 'vertical'"
          :class="[index === 0 && 'col-span-full']"
          variant="naked"
          :ui="{
            description: 'line-clamp-2'
          }"
        />
      </UBlogPosts>
    </UPageBody>
  </UContainer>
</template>
