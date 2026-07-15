
# Latent Studio: A User-Facing Image Generation Web App
Semester Final Project for Creative Coding Advanced (BA)

---

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
