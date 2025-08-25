# Brain Tumor Segmentation through HUMoR: Hybrid UNETR and MoE

---

## 📜 Abstract

To address computational bottlenecks in medical image segmentation, we introduce **HUMoR** (Hybrid UNETR and MoE), a novel architecture that integrates a Mixture of Experts (MoE) into the UNETR encoder. By replacing the need for vertical layer stacking with specialized horizontal expert subnetworks and a dynamic gating network, HUMoR accelerates convergence while maintaining high segmentation accuracy.

To improve computational efficiency and make the model lightweight for real-world deployment, we employ knowledge distillation. This technique transfers the learned knowledge from the large MoE-enhanced UNETR (teacher model) to a compact UNETR-based student model. This process successfully **reduced the model size by 75%** while preserving segmentation performance, demonstrating the potential of combining MoE architectures with knowledge distillation to create scalable and accurate solutions for medical imaging.

**Keywords:** `Attention guided UNET`, `Tumor Segmentation`, `Mixture of Experts (MoE)`, `UNETR`, `Knowledge Distillation`, `Medical Imaging`.

---

## 🚀 Introduction & Motivation

Accurate and efficient segmentation of medical images, particularly for identifying anomalies like brain tumors, is a significant challenge. The primary hurdles include the low availability of annotated data and the high computational cost of state-of-the-art deep learning models (FCNN, UNET, UNETR). While these models achieve impressive performance, their deployment in clinical settings is often hindered by hardware restrictions, latency constraints, and operational costs.

Simply adding more layers to an architecture like UNETR leads to diminishing returns, increasing computational load without proportional gains in segmentation quality. Our work addresses these challenges by:
1.  **Introducing a Mixture of Experts (MoE) framework** within the UNETR encoder to increase model capacity efficiently without stacking layers.
2.  **Applying Knowledge Distillation** to transfer the intelligence of our large, powerful model to a much smaller, faster student model, making it suitable for deployment in resource-constrained environments.

---

## 🧠 Core Concepts

Our work builds upon several foundational deep learning architectures and techniques:

*   **UNET:** A widely used U-shaped architecture for biomedical image segmentation, featuring a symmetric encoder-decoder structure with skip connections to combine contextual and spatial information.
*   **Vision Transformer (ViT) & UNETR:** UNETR adapts the transformer architecture for 3D medical image segmentation. It tokenizes volumetric patches and feeds them into a transformer-based encoder, leveraging self-attention to capture global context effectively.
*   **Mixture of Experts (MoE):** A modular architecture that divides a complex task among specialized subnetworks ("experts"). A gating network dynamically routes inputs to the most relevant experts, allowing the model to scale its capacity with only a marginal increase in computational cost per inference.
*   **Knowledge Distillation (KD):** A model compression technique where a smaller "student" model is trained to mimic the behavior of a larger, pre-trained "teacher" model. This allows the student to achieve performance far superior to what it could achieve by training on hard labels alone.

---

## 🤖 The HUMoR Architecture

HUMoR is a segmentation model designed for faster and more accurate brain tumor segmentation. It integrates an MoE framework into the UNETR transformer encoder and leverages knowledge distillation for model compression.

### 1. The Teacher Model (MoE-Enhanced UNETR)

The core of our research is the teacher model, which features a UNETR-based encoder integrated with MoE blocks.

*   **Encoder Structure:** The encoder consists of 12 transformer blocks divided into 4 groups, with **3 expert subnetworks** in each group.
*   **Input Processing:** The input image (trained on 3,064 MRI scans) is partitioned into non-overlapping 16x16 patches. These patches are linearly embedded and fed into the transformer blocks.
*   **Transformer Blocks:** Each block applies multi-head self-attention (6 heads) followed by an MLP. The MoE mechanism is integrated within the MLP layer.
*   **Decoder Structure:** A CNN-based decoder upsamples the feature maps from the encoder bottleneck. Skip connections from the encoder's MoE blocks provide high-resolution feature maps to the decoder, enabling precise segmentation.

<img width="834" height="624" alt="image" src="https://github.com/user-attachments/assets/b061341c-2aca-47ad-a579-875333a1c7ea" />


### 2. The Gating Mechanism & Mixture of Experts (MoE)

The gating network is responsible for dynamically routing input patches to the most suitable expert(s) in each MoE block.

1.  **Summarize Input:** Features are summarized via average pooling.
2.  **Generate Scores:** The pooled representation is passed through a small neural network to produce a raw score for each expert.
3.  **Load Balancing:** An auxiliary load-balancing loss is applied to encourage the gating network to distribute the load evenly across all experts, preventing over-specialization.
4.  **Select Experts:** A `softmax` function converts the scores into a probability distribution, which is used to weigh the outputs of the experts.

This selective activation ensures that only a fraction of the model's parameters are used for any given input, leading to significant computational savings.

<img width="561" height="601" alt="image" src="https://github.com/user-attachments/assets/adc49f53-dd3c-4265-8e0c-6e23d193d9fe" />



### 3. Knowledge Distillation for Model Compression

While the MoE-based teacher model is powerful, it can still be too large for clinical deployment. To address this, we use knowledge distillation:

*   **Teacher:** The large, trained HUMoR model with the MoE-UNETR encoder.
*   **Student:** A much smaller, standard UNETR model.
*   **Process:** The student model is trained to not only predict the correct segmentation masks but also to replicate the soft probability outputs of the teacher model. This "dark knowledge" transfer allows the compact student model to learn the rich feature representations and decision boundaries discovered by the teacher.
*   **Result:** This approach reduces the model size by **75%** while maintaining segmentation performance on par with the teacher, making it a practical solution for real-world applications.

---

## ✨ Key Contributions

*   **Novel Architecture (HUMoR):** We successfully integrate a Mixture of Experts (MoE) framework into a UNETR encoder for brain tumor segmentation, improving model capacity efficiently.
*   **Computational Efficiency:** Our MoE-based approach enables horizontal scaling (adding more experts) instead of inefficient vertical scaling (adding more layers), leading to faster convergence.
*   **Effective Model Compression:** We demonstrate that knowledge distillation can compress a large MoE-based teacher model into a lightweight student model, achieving a **75% size reduction** without a significant drop in accuracy.
*   **Practical Application:** The resulting lightweight model is optimized for deployment in clinical settings where computational resources and inference speed are critical constraints.

---

**Authors:**
*   Soham Jyoti Mondal
*   Harshvardhan Sthapak
*   Vaidehi Vijaykumar
*   Ritesh Pradhan

---

## 📝 License

This project is licensed under the [MIT License](LICENSE).
