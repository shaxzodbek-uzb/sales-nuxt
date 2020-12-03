<template>
  <div>
    <Navigation />
    <main>
      <div class="max-w-7xl mx-auto py-6 sm:px-6 lg:px-8">
        <!-- Replace with your content -->
        <div class="px-4 py-6 sm:px-0">
          <Nuxt />
        </div>
        <!-- /End replace -->
      </div>
    </main>
  </div>
</template>
<script>
import Navigation from '@/components/Navigation'

export default {
  components: { Navigation },
}
</script>
