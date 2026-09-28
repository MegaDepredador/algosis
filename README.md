Sí. Lo adapté para que el sistema funcione como hablamos:

Perchance recibe solamente el Appearance Prompt como base visual.
El Core Prompt nunca se envía a Perchance.
El nombre del personaje tampoco se envía.
Se mantiene un historial local de la conversación.
El código reconstruye un estado visual acumulado a partir de ese historial.
Si decís “ponete el gorro de vaquera”, se agrega cowboy hat.
Si después decís “sacate el gorro”, se elimina del estado y se añade al negativo.
La escena actual se construye usando el último mensaje del usuario + la respuesta de Muah.
Se fuerza una sola ilustración, evitando hojas de referencia, turnaround, múltiples vistas, etc.
La parte de captura de imágenes por Network queda igual porque ya comprobamos que funciona.
index.js — Discord + Muah + Perchance con estado visual

Hay una diferencia importante respecto al código anterior: ahora el Core Prompt puede seguir siendo de 7648 caracteres y no importa, porque no participa en construirPromptImagen().

Por ejemplo, si la conversación llega a:

ponete el gorro de vaquera

el log debería mostrar algo parecido a:

ESTADO VISUAL ACTUAL:
Current accessories: cowboy hat

y en Perchance aparecerá dentro del prompt:

Current established visual state:
Current accessories: cowboy hat

Si después decís:

sacate el gorro

el estado debería pasar a:

The following previously used visual elements have been removed and must NOT appear: cowboy hat

y además cowboy hat se agrega al negative prompt.

Una cosa importante: esta primera versión usa reglas de código para detectar cambios visuales; no es todavía otra IA que "comprenda" perfectamente cualquier frase. Por eso hice el sistema acumulativo y extensible: podemos ir agregando reglas para ropa, accesorios, poses, lugares, etc., sin tocar la conexión que ya funciona.

Sí. El problema principal está en que el prompt describe una “character sheet” / “model sheet” y además dice full-body character sheet presentation, lo que puede hacer que Perchance interprete que debe mostrar varias vistas del mismo personaje.

Hay que reforzar dos cosas:

Una sola personaje, una sola imagen, una sola vista.
Eliminar completamente cualquier lenguaje de character sheet, reference sheet, multiple views, etc.
Prompt corregido
Adult female anthropomorphic hyena beastwoman, tall and athletic, approximately 170 cm proportions, strong feminine physique, confident presence, long flowing vivid blue hair, thick slightly tousled blue hair, distinctive rounded hyena ears with short tufts of fur at the tips, warm amber-brown eyes, brown hyena-like fur and skin coloration, feminine anthropomorphic facial structure, mature adult appearance, strong jawline softened by feminine features, expressive eyes, confident expression.

Tall proportions, long legs, strong thighs, sturdy athletic limbs, defined but feminine shoulders, narrow defined waist, broad rounded hips, full rounded buttocks, full bust, balanced hourglass silhouette, curvy athletic body, substantial hips relative to the waist, full chest proportional to the body, strong lower body, realistic anatomical proportions, mature feminine anatomy, physically powerful appearance rather than delicate or petite.

The body should look like a strong adult beastwoman rather than an exaggerated pin-up character. Bust and hips are prominent but remain anatomically believable and proportional to her height. The waist is noticeably narrower than the ribcage and hips. The hips are broad and rounded, with strong thighs connecting naturally to the pelvis. The buttocks are rounded and full but not comically oversized. The bust is full and naturally shaped, balanced with the shoulders, waist and hips. No extreme or impossible body distortion.

Brown hyena coloration across the visible fur and skin areas, subtle darker markings and natural tonal variation inspired by spotted hyena anatomy, blue hair providing a strong contrast against the brown body coloration. Amber-brown irises, dark pupils, expressive face.

Recognizable clothing: tiny black bikini-style top, short blue denim shorts, exposed shoulders, exposed midriff, simple practical design, minimal accessories. Clothing should resemble the established anime character design rather than modern fashion.

Pose: relaxed but confident standing pose, weight naturally distributed on one leg, shoulders relaxed, head slightly tilted, confident expression, subtle mischievous smile, arms naturally positioned, body language suggesting someone physically capable and self-assured.

Visual style: high-quality Japanese anime illustration, clean anime linework, detailed anthropomorphic anatomy, polished cel shading, detailed blue hair strands, detailed hyena ears, expressive amber-brown eyes, consistent character proportions, clean silhouette, finished anime artwork.

Character identity should be communicated primarily through the combination of long blue hair, rounded tufted hyena ears, amber-brown eyes, brown hyena features, tall athletic feminine body, strong legs, broad hips, full bust, short denim shorts and black bikini-style top.

Current scene:
hola linda

Current actions, body language, facial expression and situation:
She crosses her arms and looks at you with one eyebrow raised, smiling with the typical confident mischievous smile of a hyena. She has a playful and self-assured expression while responding to the compliment.

She is standing naturally in the current scene, looking toward the viewer. The pose and expression should clearly reflect the described situation.

Create ONE single anime-style illustration of ONE single character. Show only one character in the entire image. Show the character only once. Use one continuous composition and one camera viewpoint. The image must depict a single moment in time, not multiple poses or multiple views. Keep the established physical appearance and all currently active clothing and accessories consistent with the conversation. Use a natural expressive pose appropriate to the current situation. Use an appropriate camera angle, body position, facial expression, environment, lighting and composition. The image must look like a finished anime illustration.
Negative prompt

Yo reemplazaría tu negative prompt actual por uno más específico:

multiple characters, two characters, three characters, four characters, duplicate character, duplicated person, cloned character, multiple copies of the same character, multiple instances of the character, character duplication, crowd, group, ensemble, multiple people, multiple subjects, multiple poses, multiple views, multiple angles, different angles of the same character, front view and side view together, side view and back view together, rear view together, turnaround, character turnaround, character sheet, reference sheet, model sheet, expression sheet, pose sheet, concept sheet, split screen, collage, panels, comic panels, image grid, contact sheet, comparison image, before and after, sequence of poses, repeated figure, repeated body, extra body, extra head, extra torso, extra arms, extra legs, duplicate limbs, detached limbs, deformed anatomy, bad anatomy, malformed body, distorted body, cropped character, cut off character, low quality, worst quality, blurry, pixelated, watermark, text, logo

La parte más importante es esta:

ONE single anime-style illustration of ONE single character. Show only one character in the entire image. Show the character only once. Use one continuous composition and one camera viewpoint.

Y en negativo:

multiple characters, duplicate character, multiple copies, multiple views, character sheet, turnaround, reference sheet, split screen, collage, image grid

También eliminé:

full-body character sheet presentation, neutral unobtrusive background

porque “character sheet presentation” era justamente una mala señal para el generador.

Con esto, si después agregamos el sistema del gorro blanco, el prompt puede quedar simplemente como:

... short blue denim shorts, black bikini-style top, wearing a white hat ...

sin generar cuatro versiones de Elza desde distintos ángulos.

hoy 9:30 p.m.
descubri el problema y es que al agregar el historial en el prompt de el perchance quiere crear al otro con el que esta hablando pero como en ningun momento agregamos un prompt para el otro personaje, duplica al personaje principal ( yo no quiero que haya otro personaje en la generacion solo el pj principal ) por eso pense en que no tenemos que agregar el historial ya que con la generacion de imagenes que trabajamos es bastante primitiva en sentido de que solo se guia por las descripciones visuales por ejemplo si tu le dices que cree a pikachu no hara nada, le tienes que decir como se ve visualmente y no hablo de decirle: crea un pj alto con barba. no, hablo de: personaje alto, barba, pecas en la cara. que podriamos hacer en esta situacion ya que no podemos poner el historial por que genera imagenes mal?


Sí, ahora queda mucho más claro el problema. No necesitamos enviarle el historial conversacional a Perchance. De hecho, con el tipo de modelo que estás usando, eso perjudica el resultado.

La solución que te conviene es separar completamente memoria conversacional de estado visual.

Lo que haría

Perchance debería recibir únicamente:

Appearance Prompt base
cabello
ojos
orejas
coloración
cuerpo
ropa habitual
etc.
Estado visual actual
gorro blanco
campera negra
sentada
sonriendo
brazos cruzados
etc.
Una instrucción fija para composición
un solo personaje
una sola vista
una sola pose
etc.

Nada de:

conversación completa
mensaje del usuario
respuesta de Muah
nombres
Core Prompt
diálogo
información sobre la persona con la que habla
La clave: crear una "memoria visual"

En vez de guardar el historial:

Usuario: hola linda
Elza: ¡Hola~! ¿Linda?...
Usuario: ponte un gorro blanco
Elza: claro...
Usuario: ahora siéntate
Elza: ...

el bot mantiene algo como:

estadoVisual = {
    accesorios: [
        "white hat"
    ],
    ropa: [],
    pose: [
        "sitting"
    ],
    expresion: [
        "confident smile"
    ],
    acciones: []
};

Y Perchance solamente recibe:

[APPEARANCE PROMPT]

Current visual state:
wearing a white hat
sitting
confident smile

Create ONE single anime-style illustration of ONE single character.
Show only one character.
Show the character only once.
One camera viewpoint.
One pose.
One body.
No additional characters.

Así "hola linda" no significa absolutamente nada visualmente para Perchance.

¿Y cómo sabe el bot que "ponte un gorro blanco" significa agregar un gorro?

Ahí podemos hacer algo mucho más sencillo que meter otra IA.

Podemos hacer un analizador visual local.

Por ejemplo:

Detectar objetos que se ponen
ponte un gorro blanco
ponte un sombrero negro
ponte gafas
ponte una campera roja
ponte un vestido azul

El bot detecta:

ponte + [objeto]

y agrega ese objeto al estado.

Por ejemplo:

"ponte un gorro blanco"

↓

estadoVisual.accesorios
→ ["white hat"]
Detectar eliminación

Si después decís:

quítate el gorro

el bot busca algo relacionado con gorro y elimina:

["white hat"]

Entonces la imagen siguiente vuelve a ser:

sin gorro
Y podemos hacer lo mismo con ropa

Por ejemplo:

Mensaje:

ponte una campera negra

Estado:

clothing:
- black jacket

Después:

quítate la campera

Estado:

clothing:
- vacío
También para expresiones
sonríe

↓

expression:
smiling

Después:

deja de sonreír

↓

expression:
neutral expression
Y poses
siéntate

↓

pose:
sitting

Después:

levántate

↓

pose:
standing
Lo importante: no guardar frases completas

No quiero que el sistema haga esto:

estadoVisual:
"Elza se pone un gorro blanco porque el usuario se lo pidió..."

Porque volveríamos a tener lenguaje innecesario.

Quiero que transforme las órdenes en descripciones visuales extremadamente simples:

white hat
black jacket
sitting
arms crossed
smiling

Eso es exactamente el tipo de información que el generador entiende mejor.

Incluso podemos hacerlo por categorías

Podríamos tener:

let estadoVisual = {
    accesorios: [],
    ropa: [],
    pose: [],
    expresion: [],
    cabello: [],
    cuerpo: [],
    otros: []
};

Entonces:

«ponte un gorro blanco»

→ accesorios

«ponte una campera negra»

→ ropa

«siéntate»

→ pose

«sonríe»

→ expresion

Y el prompt final se construye automáticamente.

El prompt final sería algo así

Si tu Appearance Prompt dice:

Adult female anthropomorphic hyena beastwoman...
long flowing vivid blue hair...
rounded hyena ears...
amber-brown eyes...
brown fur...
tall athletic feminine body...
black bikini-style top...
blue denim shorts...

y vos dijiste:

ponte un gorro blanco

y después:

cruza los brazos

el generador recibe solamente:

[APPEARANCE PROMPT]

Current visual state:
white hat
arms crossed

ONE single adult female anthropomorphic character.
ONE body.
ONE head.
ONE face.
ONE pose.
ONE camera viewpoint.
ONE continuous image.
No other characters.
No duplicate character.

No recibe "hola linda", no recibe la respuesta de Elza, no recibe "guapo", no recibe que está hablando con alguien.

Eso elimina precisamente el problema que acabás de descubrir.

Y hay otra ventaja

Podemos hacer que el estado visual se actualice únicamente cuando el mensaje contiene una modificación visual.

Por ejemplo:

hola linda

No cambia nada.

qué linda estás

No cambia nada.

jajaja

No cambia nada.

ponte un gorro blanco

✅ agrega gorro.

ahora ponte una campera negra

✅ agrega campera.

quítate el gorro

✅ elimina gorro.

siéntate

✅ cambia pose.

Esto sería mucho más apropiado para el Perchance que estamos usando que intentar darle contexto conversacional.

Yo haría exactamente este sistema, manteniendo tu Appearance Prompt como la identidad visual permanente y creando aparte un estadoVisual que solamente contenga cambios visuales explícitos.

okey

Perfecto. Entonces la arquitectura queda así:

Muah = conversación y personalidad.
Estado visual = memoria de cambios visuales.
Perchance = solo descripción visual final.

Y eliminamos por completo el historial conversacional del prompt de Perchance para evitar que genere un segundo personaje.

El siguiente paso sería modificar tu index.js para implementar ese estadoVisual y que entienda cosas como «ponte un gorro blanco» → agregarlo, «quítate el gorro» → eliminarlo, etc.
