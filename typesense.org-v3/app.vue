<template>
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>

<style>
:root {
  --site-padding: calc(128px + 24px);
  --section-gap: 200px;
  --site-top-padding: calc(100px + 72px);
}
@media not all and (min-width: 768px) {
  :root {
    --site-padding: 16px;
    --section-gap: 120px;
    --site-top-padding: calc(72px + 56px);
  }
}
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
html,
body {
  font-family: "Inter", sans-serif;
  font-weight: 300;
}
h1,
h2,
h3,
h4 {
  @apply font-heading font-medium capitalize;
  line-height: 1.2;
  letter-spacing: -1.44px;
}
h1 {
  @apply max-sm:!text-[40px];
  font-size: 56px;
}
h2 {
  @apply max-sm:!text-[32px];
  font-size: 48px;
}
h3 {
  @apply max-md:!text-[24px];
  font-size: 32px;
}
h4 {
  @apply max-sm:!text-[20px];
  font-size: 24px;
}
h1 strong,
h2 strong {
  @apply text-primary;
  font-weight: inherit;
}
b {
  font-weight: 500;
}

.site-padding {
  padding: 0 var(--site-padding);
  display: flex;
  flex-direction: column;
  gap: var(--section-gap);
}
.site-top-padding {
  margin-top: var(--site-top-padding);
}
.scrollbar-hidden {
  scrollbar-width: none; /* Firefox */
}
.scrollbar-hidden::-webkit-scrollbar {
  display: none; /* Chrome, Safari */
}
</style>
