<template>
  <div class = "bg-[#111827] min-h-screen">
    <!-- Wrap components in Suspense to handle loading -->
    <Suspense>
      <template #default>
        <!-- Wrap all Components inside a single root element -->
          <div>
            <NavBar />
            <HeroSection />
            <Student />
            <Subject />
            <Footer />
            <BackToTop />
          </div>
        </template>
        <template #fallback>
          <div class="flex justify-center items-center min-h-screen">
              <loadingSpinner />
          </div>
        </template>
    </Suspense>    


  </div>
</template>

<script setup>
 import { defineAsyncComponent } from 'vue';
//Import Components Asynchronously
const NavBar = defineAsyncComponent(()=>import('@/components/NavBar.vue'));
const HeroSection = defineAsyncComponent(()=>import('@/components/HeroSection.vue'));
const Footer = defineAsyncComponent(()=>import('@/components/Footer.vue'));
const BackToTop = defineAsyncComponent(()=>import('@/components/BackToTop.vue'));
const Student = defineAsyncComponent(() => import('@/components/Student.vue'));
const Subject = defineAsyncComponent(() => import('@/components/Subject.vue'));
import loadingSpinner from './components/loadingSpinner.vue';
</script>

<style>

*{
  scrollbar-width: thin;
  scrollbar-color: #111827 #f1f1f1;
}
</style>