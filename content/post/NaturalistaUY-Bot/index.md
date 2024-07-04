---
title: El bot de NaturalistaUY
subtitle: Lo esencial es invisible a los ojos, pero no a los bots
summary: Lo esencial es invisible a los ojos, pero no a los bots
authors:
  - admin
tags:
  - iNaturalist
  - API

categories: []
projects: []
# Date published
date: '2024-05-27T00:00:00Z'
# Date updated
lastMod: '2024-05-27T00:00:00Z'
# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: ''
  focal_point: ''
  placement: 2
  preview_only: false

links:
  - name: NaturalistaUY bot
    icon: robot
    icon_pack: fa
    url: https://ecoevo.social/@Naturalista@bots.uy
---

Todos los días se suben en promedio 75 observaciones a NaturalistaUY. Muchas de estas observaciones registran especies comunes de nuestro país como puede ser el hornero (*Furnarius rufus*) o el mburucuyá (*Passiflora caerulea*), pero cada tanto también se registran especies sumamente raras y únicas para Uruguay y el mundo. ¿Cómo podemos seguirle el paso a las y los usuarios en la plataforma y documentar estos hallazgos?

Una manera sería entrar todos los días, hacer una lista de las especies registradas y después comparar una por una contra todo lo que ya se ha registrado en el país (¿y en el mundo?). ¡Qué difícil! Otra manera, es crear un bot.

Los bots son programas informáticos que hacen tareas repetitivas de manera automática a través de una cadena de comandos, por ejemplo teniendo la capacidad de responder a un estímulo. En nuestro caso, la tarea reiterativa sería comparar cada registro con todos los registros que se llevan hechos hasta ahora y el estímulo sería las nuevas observaciones que se generan día a día.

Pero ¿por dónde empezar? Lo primero, [es explorar la API de iNaturalist](https://flograttarola.com/post/inat_api_new_records/). Las APIs no son variedades de cerveza (aunque podrían). En un 'te lo resumo así nomás' podríamos decir que las APIs nos permiten comunicarnos con la base de datos online de una plataforma para, por ejemplo, hacerles preguntas, o *queries*, sobre sus datos. iNaturalist, así como **GBIF** (Sistema Global de Información sobre Biodiversidad), la **Lista Roja de la UICN** (Unión Internacional para la Conservación de la Naturaleza) o **Wikipedia**, tienen su propia API que documentan y disponibilizan para que las personas puedan usarlas. El propio sitio web de iNat ejecuta esas llamadas (*queries*) cada vez que un usuarie hace click en el menu 'Explorar' observaciones en Uruguay. Entonces el sitio web devuelve la cantidad de observaciones, especies, observadores e identificadores para Uruguay. De la misma forma, pero quitando la visualización de la información, podemos obtener estos datos en formato de texto plano, como es JSON. Por ejemplo, esta url https://api.inaturalist.org/v1/observations/?place_id=7259&created_on=2024-05-01 y esta https://www.naturalista.uy/observations?created_on=2024-05-01&place_id=7259&verifiable=any, nos devuelven los mismos resultados. En el primer caso, en formato de texto plano y en el segundo en forma de página web. Al texto plano lo podemos guardar, partir y analizar por ejemplo, usando programas informáticos, para extraer la información que nos interesa. En nuestro caso, para conocer qué especies se documentaron en un día y cuantas veces se habían documentado antes para nuestro país.

Volviendo al bot, el siguiente paso una vez que entendemos cómo funciona la API de iNaturalist es [pedir ayuda desesperadamente](https://ecoevo.social/@flograttarola/112083586968140650) a gente que haya hecho algo parecido.

<iframe src="https://ecoevo.social/@flograttarola/112083586968140650/embed" class="mastodon-embed" style="max-width: 100%; border: 0" width="400" allowfullscreen="allowfullscreen"></iframe><script src="https://mastodon.social/embed.js" async="async"></script>

Y acá es que llega [Julio J.](https://mastodon.uy/@j3j5) al rescate, un gran botólogo (experto en bots). Un genio de la automatización de llamadas a APIs y a bases de datos online, con célebres bots como el 'IPAPBot, índice de precios del asado con picada', '[BigBo energy](https://bots.uy/BigBo_Energy), estadísticas de generación eléctrica en Uruguay' o su más reciente, '[BigBo wiki](https://bots.uy/bigbo_wiki_es), los artículos más vistos en Wikipedia en español'. Un par de viernes de llamadas virtuales (en husos horarios compatibles entre Praga y Montevideo) y mucha magia después, las primeras pruebas salieron a la luz, *powered by* [tacuruses](https://gitlab.com/j3j5/tacuruses). Una serie de llamadas a la API, datos que se extraen, un mensaje que se despliega, con un espacio más acá, otro allá, emojis varios y pum, humo verde, habemus bot.

En definitiva, ¿qué hace el susodicho? El bot de NaturalistaUY está programado para revisar registros día a día y destacar aquel que documente la especie más rara del día. Esto lo hace calculando cuántas veces se había registrado antes esa especie en Uruguay y cuántas en iNaturalist, es decir, en la plataforma en general. Hacia adelante, también queremos poder destacar observadores, identificadores, lugares de nuestro país y proyectos.

<!-- ![https://bots.uy/naturalista](bot-avatar.png) -->

Así que ya sabés, ponete el recordatorio y vichá el bot de NaturalistaUY a las 10 am Uruguay para tener el registro del día. Yo que vos quedo atentx, **puede que sea un registro de una especie nueva para el país**.

<!-- <blockquote lang="es" cite="https://bots.uy/p/Naturalista/182954630366498816">
<p><a href="https://www.naturalista.uy/observations/216596499" rel="nofollow noopener noreferrer" target="_blank">Registro del día en 🇺🇾</a><br>Esta fue la especie con menos observaciones registradas en Uruguay el sábado pasado (2024-05-18):<br><br><b>Pardela de Cabo Verde (<i>Calonectris edwardsii</i>), Aves 🦜.</b><br><br></p>
<blockquote>Observada por <a href="https://www.naturalista.uy/people/martzz" rel="nofollow noopener noreferrer" target="_blank">martzz</a> el sábado 18 de mayo de 2024.</blockquote>
  <p><br>Esta especie es nativa de Uruguay, está amenazada y <b>¡es la primera vez que se registra en el país</b> (aunque se registró 37 veces en el resto del mundo)!</p>
  <footer>
     — Naturalista 🇺🇾 Bot (@Naturalista@bots.uy) <a href="https://bots.uy/p/Naturalista/112513553622894514"><time datetime="2024-05-27T16:36:22.000Z">27/05/2024, 16:36:22</time></a>
  </footer>
</blockquote>

<br> -->

<iframe src="https://bots.uy/Naturalista/185763121258958848/embed" class="tacuruses-embed" style="max-width: 100%; border: 0" width="400" allowfullscreen="allowfullscreen"></iframe><script src="https://bots.uy/embed.js" async="async"></script>
