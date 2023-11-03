<template>
  <svg width="100%" height="100vh">

    <g id="leftGamepad" style="transform:translate(25%, 50%)">
      <circle id="outerCircleLeftGamepad" r="20%" stroke="red" />
      <circle id="innerCircleLeftGamepad" r="10%" fill="red" />
    </g>

    <g id="rightGamepad" style="transform:translate(75%, 50%)">
      <circle id="outerCircleRightGamepad" r="20%" stroke="red" />
      <circle id="innerCircleRightGamepad" r="10%" fill="red" />
    </g>
    <rect id="leftRect" fill="transparent" x="0" y="0" width="50%" height="100vh" />
    <rect id="rightRect" fill="transparent" x="50%" y="0" width="50%" height="100vh" />

  </svg>
</template>

<script setup lang="ts">
import { onMounted } from 'vue';
import { Arcane, ArcaneBaseEvent } from 'arcanepad-web-sdk'

onMounted(async () => {
  Arcane.init()


  function leftStick() {
    const leftRect = document.getElementById("leftRect") as HTMLElement
    const cont = document.getElementById("leftGamepad") as HTMLElement
    // const outerCircle = document.getElementById("outerCircleLeftGamepad")
    const innerCircle = document.getElementById("innerCircleLeftGamepad") as HTMLElement

    let startX = 0
    let startY = 0
    let currentX = 0
    let currentY = 0

    leftRect.addEventListener('touchstart', (e) => {
      startX = currentX = e.targetTouches[0].clientX
      startY = currentY = e.targetTouches[0].clientY

      cont.style.transform = `translate(${startX}px, ${startY}px)`
    })
    leftRect.addEventListener('touchmove', (e) => {
      const x = e.targetTouches[0].clientX
      const y = e.targetTouches[0].clientY
      const diffX = x - startX
      const diffY = y - startY

      innerCircle.setAttribute('cx', diffX + 'px')
      innerCircle.setAttribute('cy', diffY + 'px')

      const angle = Math.atan2(diffY, diffX) * 180 / Math.PI + 180

      const dir = Math.floor((angle + 22) / 45)

      if (angle < 22.5 && angle > 337.5) Arcane.msg.emitToViews(new ArcaneBaseEvent('moveLeft'))
      else if (dir == 0) Arcane.msg.emitToViews(new ArcaneBaseEvent('moveLeft'))
      else if (dir == 1) Arcane.msg.emitToViews(new ArcaneBaseEvent('moveLeftUp'))
      else if (dir == 2) Arcane.msg.emitToViews(new ArcaneBaseEvent('moveUp'))
      else if (dir == 3) Arcane.msg.emitToViews(new ArcaneBaseEvent('moveRightUp'))
      else if (dir == 4) Arcane.msg.emitToViews(new ArcaneBaseEvent('moveRight'))
      else if (dir == 5) Arcane.msg.emitToViews(new ArcaneBaseEvent('moveRightDown'))
      else if (dir == 6) Arcane.msg.emitToViews(new ArcaneBaseEvent('moveDown'))
      else if (dir == 7) Arcane.msg.emitToViews(new ArcaneBaseEvent('moveLeftDown'))
      else if (dir == 8) Arcane.msg.emitToViews(new ArcaneBaseEvent('moveLeft'))

    })
    leftRect.addEventListener('touchend', (e) => {

      cont.style.transform = `translate(25%, 50%)`

      innerCircle.setAttribute('cx', '0')
      innerCircle.setAttribute('cy', '0')

      Arcane.msg.emitToViews(new ArcaneBaseEvent('stopMoving'))

    })
  }
  leftStick()

  function rightStick() {

    const rightRect = document.getElementById("rightRect") as HTMLElement
    const cont = document.getElementById("rightGamepad") as HTMLElement
    // const outerCircle = document.getElementById("outerCircleLeftGamepad")
    const innerCircle = document.getElementById("innerCircleRightGamepad") as HTMLElement

    let startX = 0
    let startY = 0
    let currentX = 0
    let currentY = 0

    rightRect.addEventListener('touchstart', (e) => {
      startX = currentX = e.targetTouches[0].clientX
      startY = currentY = e.targetTouches[0].clientY

      cont.style.transform = `translate(${startX}px, ${startY}px)`
    })
    rightRect.addEventListener('touchmove', (e) => {
      const x = e.targetTouches[0].clientX
      const y = e.targetTouches[0].clientY
      const diffX = x - startX
      const diffY = y - startY

      innerCircle.setAttribute('cx', diffX + 'px')
      innerCircle.setAttribute('cy', diffY + 'px')

      const angle = Math.atan2(diffY, diffX) * 180 / Math.PI + 180

      Arcane.msg.emitToViews({ name: 'rotate', x: diffX * 1000, y: diffY * 1000 } as any)

    })
    rightRect.addEventListener('touchend', (e) => {

      cont.style.transform = `translate(75%, 50%)`

      innerCircle.setAttribute('cx', '0')
      innerCircle.setAttribute('cy', '0')

    })
  }
  rightStick()

})

// let debugConsole = document.createElement('div');
// debugConsole.style.cssText = 'position:absolute;color:green;top:0;left:0:right:0:bottom:0';
// document.body.appendChild(debugConsole);

</script>
