# Latent Studio: A User-Facing Image Generation Web App
Semester Final Project for Creative Coding Advanced (BA)


## Projekt-Metadaten

- **Name:** Sarah Moses
- **Course:** Creative Coding Advanced (BA)
- **Semester:** Summer Semester 2026
- **University** Technische Hochschule Nürnberg Georg Simon Ohm (Design)
- **Professor:** Moritz Schwind
- **Live-Demo (Colab):** [Link zu deinem geteilten Colab Notebook]
- **Web-App Link:** [Link zur temporären Gradio-Share-Demo, falls aktiv]

---

## Background

Over the past few years artificial intelligence has become more and more present in our daily lives. Many found new opportunities in this evolving technology, from getting general tasks done faster to supporting the creative process. Since AI became mainstream image generators flooded the internet. Nowadays everybody is able to generate a picture from seemingly nothing. However most of these generators only let the user input a text description (a so called “prompt”) for the motive they want to create. In these applications the user might be able to change the model or aspect ratio, but further control over the resulting image is often lacking. To counter that the web app ***Latent Studio*** gives the user more possibilities to influence the final result. With easy to understand explanations accompanying the customizable inputs, the web app gives the user the opportunity to understand the process of image generation better, making process more purposeful.

---

## Abstract

***Latent Studio*** is a web app created with the help of Google Collab, Gradio, Cursor and Gemini. It is based on the architecture of ***Stable Diffusion 1.5*** and focuses on text-to-image generation. The app gives the user not only control over the prompt: To optimize the user experience, it exposes several easy to understand parameters within a clean interface, for more control over the resulting image.

The user is able to switch between the basic Stable Diffusion **model** which sets a well trained foundation and the Dreamshaper model (created by Lykon) for more stylized creations.

On top of the model, the user can choose to add a smaller fine tuned sub-model to get a specific look: The so called Low-Rank Adaptation also known as **LoRA**. How much the selected LoRA influences the result can be changed via the slider “LoRA Strength”, that appears after selection.

After that the user can write a **prompt** for what they want to see in the image and a **negative prompt** for what they want to avoid. If a LoRA is selected, the according trigger word will automatically get added to the prompt.

How many times the AI should improve the image can be changed with the “**Steps**” slider. Next to it the user can define how strictly the AI should follow the prompt using the “**CFG**” (Classifier-Free Guidance) slider. The **seed** for each generation can be fixed for comparable results or randomized for more variety.

Lastly if the user has a specific composition or object in mind they want to include as accurately as possible, they can enable the “**ControlNet**”. Afterwards the image that should be used as reference can be uploaded. The amount of influence this image has on the result can be changed with the “Control Strength” Slider. For this specific ControlNet the Canny Edge method is used, meaning that the edges of the reference image get extracted to guide the diffusion process. The resulting Canny Edge Map gets output alongside the generated image and its meta data.

---

## Approach

The general structure of the project was vibe coded, mostly as a notebook within Google Collab with support form Google Gemini and Claude. Later in the process I also used Cursor due to free plan limitations of Google Collab. In the following I will explain the code structure that resulted in this project:

### 1. Environment Setup

The purpose of this section is to prepare and install the external code libraries necessary to run machine learning models. Since image generation is not a light task and can involve a lot of calculations, the fastest available processor needs to be detected and implemented. Since I had to switch to a local environment (and replace Google Collab temporary with Cursor) the notebook is currently configured for macOS. It detects and activates Apple Silicon GPU and falls back to NVIDIA GPU (CUDA) or your systems CPU if running somewhere else. 

### 2. Model Initialization

Here the base model Stable Diffusion 1.5 and the model Dreamshaper get loaded into the directly from the AI community platform **Hugging Face**. Furthermore the ControlNet gets initialized: It loads a **Canny Detector** preprocessor along with the Canny ControlNet model. This allows the AI to capture outlines from a reference image and use them to guide the layout of the newly generated image.

### 3. LoRA

Low Rank Adaptation (short LoRA) is a technique that lets you inject specific styles into a base model without having to retrain the entire network. They are also helpful to generate a specific object consistently with high detail. I previously trained two style LoRAs in a separate Google Collab notebook, resulting in according .safetensor files that get loaded in this section. In my dataset used for training I added text files describing every image individually. All the text files have one unique word in common, when this specific word is written inside the prompt, the LoRA gets activated.

A LoRA is especially useful, if the base model can not generate an object or image style sufficiently. I trained two specific styles that were lacking in the base model and could not be displayed as well as I would have liked. During the conceptual phase it is also important to have in mind that you need a large enough dataset, to base the training on. The images should have varying subjects in it but keep the overall style as consistently as possible so that the AI can learn what to keep.

#### Low Poly LoRA

I wanted to add a style that is reminiscent of old computer graphics but still adds a cute touch. I was specifically drawn to characters and objects from Nintendo games that were popular in the early 2000s since I grew up during that time and played those games. I found a lot of different images fitting this exact style, with bright colors and hard edges resulting from the limited number of polygons that were possible to use back then. Every image in this dataset displays one item or character on white background with the same visual style. I used 48 images in total with describing captions for each image in the form of a simple .txt file. This helps the LoRA differentiate what it should recognize as part of the style and what it should not include. This way the model does not have to guess, resulting in better training.

The trigger word I defined for this LoRA is “lpyx7 style”

*Dataset examples:*



*Example Generations:*


The Low Poly LoRA worked well with just the trained trigger word (”lpyx7 style”) added at the end of the positive prompt

*LoRA strength exploration:*



#### Found Footage LoRA

For this LoRA I wanted to see how well the specific flashlight look from my dataset will be transferred after training. Most of the images I used for training had a bright spot in one part of the image, that fades into darkness at the edges. I also payed attention to the overall color pallet of the images, since that played a big part in making the spooky atmosphere and overall style recognizable. Furthermore I tried finding different perspectives and subjects inside the images so that the LoRA knows what aspects should stay variable and what should be baked into the style. 

The trigger word I defined for this LoRA is “FndF0ot4ge”

*Dataset examples:*



*Comparision Low Poly LoRA vs no LoRA:*

*LoRA strength exploration:*

---

## Findings

#### Extra descriptive words in prompt for best LoRA results

*Low Poly LoRA:*

The Low Poly LoRA sometimes worked well with just the trained trigger word (”lpyx7 style”) added at the end of the positive prompt, but it worked best if “low poly” was written in the prompt. I believe the reason for that is, that the base model already knows the concept “low poly” which enforces the effect of the LoRA that helps generating the specific low poly style it was trained on. That is undermined by the fact, that both generations (with Low Poly LoRA vs without LoRA) using “low poly” in the prompt, look very similar. The image generated without a LoRA has slightly more detail in the background, but the subject looks very similar. The image generated with the Low Poly LoRA has harsher shadows and a solid colored background. I think that happens because every image in the training data had a simple white background. 



*Found Footage LoRA:*

The Found Footage LoRA on the other hand needs “night” in the prompt for the desired effect. Words meaning similar things like “dark” or “darkness” do not work, they just made the palm tree a bit darker and the image overall a bit less saturated (the colors get a bit closer to the trained style bot not quite enough). Using “sunlight, daytime, daylight” in the negative prompt also does not work.



### LoRA Strength

I found that a strength of 1.3 for the Low Poly LoRA produces the best results. The effect is visible even without the addition of “low poly” to the prompt. At a strength of 1.5 the image starts to break.

model_name = 'Base SD 1.5'
lora_name = 'Low Poly'
base_prompt = "a watermelon laying on a forest floor"
seed = 49
steps = 20
cfg = 7.5
num_grid_steps = 10



---

## Difficulties

### Failed LoRA

In the beginning I tried training a LoRA on the style of old CGI renderings specifically from the program Bryce. I thought it would be a nice concept to use this new AI technology to emulate the look of old computer graphics. Even though I liked the style I came to the conclusion, that a big part of the appeal these references had, came from the overall composition and surreal objects placed in the scenes and less from the basic shaders, colors or lighting. The images I found were also too different from each other so that I had a hard time choosing which ones would work. This idea evolved into the Low Poly LoRA, for which I found better reference images with consistent style.

*Bryce renderings:*

Modus Interactive's Bryce Render Pack v1 © 2022 by Modus Interactive


*Validations:*

In the beginning a lot of rifles were generated

### Trigger Word

I had difficulty defining a fitting trigger word. During my first attempt at training the Low Poly LoRA I used the instance prompt “low poly sks” which I also added to every text file I uploaded for training alongside the images. The problem with this trigger phrase was, that I could not compare the image generated with the LoRA vs without the LoRA, since “low poly” always activated the Low Poly LoRA. This meant I had to remove these words from the prompt, resulting in much more realistic generations when the LoRA is inactive. I could not compare the low poly style the Stable Diffusion model inherently comes with to my trained LoRA which is why I decided to retrain with the new trigger word “lpyx7 style”. Because the AI has no prior bias towards the new word “lpyx7” it does not influence the generated images with ideas or concept it already knows. The word “stlye” helps the AI figure out what this new word represents, supporting the training stage.

### Cropping and Background

For the Low Poly dataset I used only images of objects or characters that centered on a white background with sufficient gaps towards the borders. When selecting this LoRA and generating an image, I realized that the subject is often cropped placed way too large within the generated image. I found that using the the canny edge function in the ControlNet on a low setting can fix this issue. Also the background is never purly white, it often has a light structure or different color.
