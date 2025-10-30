<template>
  <UHeader to="/" :toggle="false">
    <template #title>
      <div class="flex pl-1 text-2xl md:text-3xl items-center">
        <Icon name="game-icons:gibbet" />
        <h1>FORCA</h1>
      </div>
    </template>
    <template #right>
      <UColorModeButton />
      <UModal>
        <UTooltip text="Info">
          <UButton color="neutral" size="xl" variant="ghost" aria-label="info">
            <Icon class="text-xl" name="line-md:alert-circle-twotone-loop" />
          </UButton>
        </UTooltip>
        <template #content>
          <div class="flex p-3 px-4 flex-col gap-2 items-center justify-center">

            <h1 class="text-xl font-bold"> Oie! tudo Bão?</h1>
            <p class="text-center">Esse projeto foi criado em Outubro de 2025 por mim, <ULink
                :class="`text-${corPrimaria}`" as="button" target="_blank" to="https://github.com/JoaoPedro77">
                JoaoJ</ULink>
            </p>
            <USeparator class="" color="tertiary" icon="fluent-emoji-high-contrast:motor-scooter" />


            <UCard variant="soft">
              <template #default>
                <p class="text-center">
                  Esse projeto foi feito sem nenhum fim lucrativo, apenas como estudo e prática de desenvolvimento web.
                </p>
              </template>
            </UCard>
            <div class="flex flex-row items-center justify-center gap-1">
              me encontre em:
              <div>
                <UButton icon="i-simple-icons-instagram" color="neutral" variant="ghost"
                  to="https://www.instagram.com/joaoj_pedroo/" target="_blank" aria-label="Instagram" />
                <UButton icon="i-simple-icons-github" color="neutral" variant="ghost"
                  to="https://github.com/JoaoPedro77" target="_blank" aria-label="GitHub" />
              </div>
            </div>

            <USeparator class="p-2" />
            <h4 class=" text-center text-xs font-bold"> Muito obrigado aos que avaliaram o projeto e deram sugestões
              durante o
              desenvolvimento:</h4>
            <div class="flex flex-wrap gap-1">
              <UBadge icon="icon-park-twotone:honey" size="md" class=" rounded-full" color="primary" variant="subtle">
                Mariii
              </UBadge>
              <UBadge icon="mdi:handshake-outline" size="md" class=" rounded-full" color="violet" variant="subtle">
                Niccsz
              </UBadge>
              <UBadge icon="tabler:coffee" size="lg" class=" rounded-full" color="miku" variant="subtle">
                Matt
              </UBadge>
              <UBadge icon="mdi:application-braces-outline" size="lg" class=" rounded-full" color="emerald"
                variant="subtle">
                Prode
              </UBadge>
            </div>


            <p class=" pt-2 text-center text-xs text-stone-400">
              Copyright © 2025 JoaoJ.
              Todos os direitos reservados.
            </p>
          </div>
        </template>
      </UModal>

    </template>
  </UHeader>

</template>

<script setup>

defineProps({
  corPrimaria: String
})

</script>