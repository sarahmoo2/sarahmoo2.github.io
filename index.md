# Latent Studio: [Dein App-Name, z. B. PromptCraft]
[cite_start]**A User-Facing Image Generation Web App** *Semester Final Project for Creative Coding Advanced (BA)* [cite: 2, 30]

---

## 👤 Projekt-Metadaten
* **Name:** [Dein Name]
* [cite_start]**Kurs:** Creative Coding Advanced (BA) [cite: 30]
* **Semester:** [z. B. Sommersemester 2026]
* [cite_start]**Hochschule:** Technische Hochschule Nürnberg Georg Simon Ohm (Design) [cite: 3]
* [cite_start]**Dozent:** Moritz Schwind [cite: 3]
* [cite_start]**Live-Demo (Colab):** [Link zu deinem geteilten Colab Notebook] [cite: 62]
* [cite_start]**Web-App Link:** [Link zur temporären Gradio-Share-Demo, falls aktiv] [cite: 50]

---

## 📝 Abstract
[cite_start][Dein App-Name] ist eine benutzerfreundliche Web-App auf Basis von **Gradio**, die das Arbeiten mit dem Text-to-Image-Diffusionsmodell **Stable Diffusion 1.5** für Nicht-Experten zugänglich macht[cite: 6, 7, 14]. [cite_start]Während die technische Pipeline im Hintergrund komplexe Operationen wie das dynamische Laden von Modellen und LoRA-Weights im ressourcenschonenden `fp16`-Modus ausführt, bietet das Interface eine intuitive, fehlerverzeihende Steuerung[cite: 7, 41, 44, 45]. [cite_start]Das Design der Benutzeroberfläche leitet Anwender gezielt durch Parameter wie CFG-Scale, Inferenzschritte und Seed-Optionen, um nachvollziehbare und reproduzierbare Ergebnisse zu garantieren[cite: 7, 10, 24, 25, 26].

---

## 🛠 Features & UI-Design
Hier beschreibst du kurz, was deine App kann und warum das UI so aufgebaut ist.

### Core Features (Auswahl)
* [cite_start]**Base Model Selector:** Nahtloser Wechsel zwischen `Stable Diffusion 1.5 Base` und `[Name deines Community Checkpoints]`[cite: 16]. [cite_start]Das Laden erfolgt "lazy", um den VRAM (T4 GPU) nicht zu überlasten[cite: 40, 44].
* **LoRA-Integration:** Dynamisches Aktivieren und Gewichten von [Anzahl, z. [cite_start]B. 2] LoRAs (inklusive eines selbst trainierten LoRAs: `[Name deines LoRAs]`)[cite: 20, 21].
* **Reproduzierbarkeit:** Jedes generierte Bild zeigt die exakten Metadaten an. [cite_start]Der Seed kann entweder fixiert oder für jede Generation zufällig neu gewürfelt werden[cite: 26, 27].
* [cite_start]**Nutzerführung (UX):** Alle Slider (CFG, Steps) sind mit kurzen Tooltips oder Beschreibungstexten versehen, die erklären, was die Regler bewirken[cite: 24, 25].

> *Optional (Falls du ADV-Teile umgesetzt hast):*
> ### Advanced Features (ControlNet)
> [cite_start]* **Spatial Conditioning:** Integration eines ControlNets (z. B. [Canny / Depth / Pose])[cite: 34]. Benutzer können ein Referenzbild hochladen, die Konturen vorverarbeiten lassen und die Stärke des Einflusses via Slider steuern[cite: 34].

---

## 🧠 Findings, Difficulties & Conceptual Ideas
*Tipp: Sei hier ehrlich! [cite_start]Der Dozent möchte sehen, was du gelernt hast und wie du Probleme gelöst hast[cite: 66].*

### Konzeptuelle Idee
* *Was war dein Design-Ansatz? (z. B. "Minimalismus", "Dark Mode zur Schonung der Augen bei langen Promp-Sessions", "Wissenschaftlicher Look").*

### Technische Herausforderungen & Lösungen
1. [cite_start]**Speicher-Management (VRAM):** * *Problem:* Beim Wechsel der großen Base-Modelle stürzte die App anfangs wegen `Out of Memory (OOM)`-Fehlern auf der T4-GPU ab[cite: 17, 44].
   * [cite_start]*Lösung:* Implementierung einer "Lazy Loading"-Funktion, die alte Pipelines via `del` und `torch.cuda.empty_cache()` explizit aus dem VRAM löscht, bevor das neue Modell geladen wird[cite: 44].
2. **Reproduzierbarkeit der Seeds:**
   * [cite_start]*Problem:* Unterschiedliche Bilder trotz identischem Seed[cite: 46].
   * [cite_start]*Lösung:* Sicherstellung, dass der `torch.Generator` fest an das Device (CUDA) gekoppelt ist und der tatsächlich genutzte Seed immer im UI zurückgegeben wird[cite: 26, 46].

---

## 📊 Parameter-Atlas (Exploration)
[cite_start]*Füge hier deine Erkenntnisse aus dem Parameter-Sweep (CFG vs. Steps und LoRA-Weights) ein[cite: 28].*

| Testreihe | Beobachtung & Erkenntnisse (In den Worten des Kurses) |
| :--- | :--- |
| **CFG-Scale Sweep** | Ein niedriger CFG-Wert (unter 5) führt zu sehr traumartigen, unpräzisen Bildern. [cite_start]Ein zu hoher Wert (über 15) führt zu Farb-Obersättigung ("burn-in") und harten Kontrasten[cite: 28]. [cite_start]Der Sweetspot liegt bei 7–9[cite: 24]. |
| **Step Count Sweep** | Unter 15 Schritten ist das Bild noch stark verrauscht (Euler a). [cite_start]Ab 25 Schritten konvergiert das Bild und zusätzliche Schritte bringen kaum noch Qualitätsgewinn, verbrauchen aber unnötig Rechenzeit[cite: 25, 28]. |
| **LoRA-Weight Sweep** | Bei einer LoRA-Gewichtung von < 0.4 ist der Stil kaum sichtbar. [cite_start]Ab > 1.2 beginnt der Stil des LoRAs die Anatomie des Base-Modells zu zerstören[cite: 20, 28]. |

---

## 🖼 Example Generations
[cite_start]*Zeige hier 2–3 deiner besten Ergebnisse mit den vollständigen Generierungsparametern, um die Reproduzierbarkeit zu beweisen[cite: 27, 67, 70].*

### Bild 1: [Titel des Bildes, z. B. Retro-Futuristic City]
`[Hier Bild einfügen, z. B. ![Retro City](images/generation_01.png)]`

| Parameter | Wert |
| :--- | :--- |
| **Base Model** | [z. [cite_start]B. DreamShaper v8 (SD 1.5)] [cite: 16] |
| **LoRA** | [z. [cite_start]B. Neon-Cyberpunk LoRA] (Weight: `0.85`) [cite: 20] |
| **Prompt** | [cite_start]`A highly detailed retro-futuristic city, neon lights, rainy night, 8k` [cite: 23, 27] |
| **Negative Prompt** | [cite_start]`blurry, low quality, distorted, humans` [cite: 23, 27] |
| **Steps** | [cite_start]`30` [cite: 25, 27] |
| **CFG Scale** | [cite_start]`7.5` [cite: 24, 27] |
| **Seed** | [cite_start]`4294967295` [cite: 26, 27] |

---

## 💳 Credits, Licenses & Attribution
[cite_start]*Hier zeigst du "GPU-Disziplin" und akademische Ehrlichkeit[cite: 48, 71].*

* [cite_start]**Stable Diffusion 1.5 Base:** Released under the [CreativeML OpenRAIL-M License](https://huggingface.co/spaces/CompVis/stable-diffusion-license)[cite: 71].
* **Community Checkpoint [Name]:** [Link zum Modell, z. [cite_start]B. auf Civitai] – Lizenziert unter [Lizenztyp][cite: 16, 22].
* [cite_start]**LoRA [Name]:** Selbst trainiert im Rahmen des Semesters mit [Dataset-Beschreibung] [cite: 21] [cite_start]/ *oder* von [Creator-Name] unter [Lizenz][cite: 22].
