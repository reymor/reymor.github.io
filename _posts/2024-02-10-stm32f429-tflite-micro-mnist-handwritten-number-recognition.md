---
layout: post
category: documentation
---

In September 2020, I was looking for a way to better understand machine learning. As I embedded software engineer, I was loooking for something related with this field and I found that tensorflow had added support for microcontrollers which is called [TensorFlow Lite for Microcontrollers](https://www.tensorflow.org/lite/microcontrollers). I enrrolled a course in [edx](https://www.edx.org/) called [Fundamentals of TinyML](https://www.edx.org/learn/machine-learning/harvard-university-fundamentals-of-tinyml). Right now, there is a serie which also includes [Application of TinyML](https://www.edx.org/learn/tinyml/harvard-university-applications-of-tinyml) and [Deploying TinyML](https://www.edx.org/learn/tinyml/harvard-university-deploying-tinyml) which we were not available at the moment that I enrolled the course.

When I finish the course, I had the idea of doing some project in which I deploy something in real hardware. I have a [STM32F429I-DISCO](https://www.st.com/en/evaluation-tools/32f429idiscovery.html) in which I thought about running some application in this board. There were many ideas but I choose handwritten number recognition using [The mnist database](http://yann.lecun.com/exdb/mnist/). I spent some weeks figuring out how to do so, there were not many examples, just [Hello World](https://github.com/tensorflow/tflite-micro/tree/main/tensorflow/lite/micro/examples/hello_world) example which predict value of sinwave using TFLM interpreter and few platform support. For real, the main tf project includes this as subfolder, right now, there is a different [repository](https://github.com/tensorflow/tflite-micro/tree/main) for this. 

I was able to finish this project and push the code to my github account with the name of [stm32f429-tflite-micro-mnist](https://github.com/reymor/stm32f429-tflite-micro-mnist). I did not spent to much time in improvement, I faced some problems related with the use of the model. I just put the effort in making everything to work. The model needs almost 2 seconds for running the model. 

![handwritten_2020](https://raw.githubusercontent.com/reymor/reymor.github.io/master/files/pics/handwritten_2020.png)

This week, I was reading an article about an optimized kernel implementations in Cortex-M and I read [CMSIS-NN](https://arxiv.org/pdf/1801.06601.pdf). At the moment that I publish this toy project there was not specific kernel for Cortex-M in TFLM, I have tried this optimized kernel and the same model runs almost ten times faster. 

![comparation_image](https://raw.githubusercontent.com/reymor/reymor.github.io/master/files/pics/graph_cmp.png)

![handwritten_2024](https://raw.githubusercontent.com/reymor/reymor.github.io/master/files/pics/handwritten_2024-cmsis-nn.png)

**CMSIS-NN** is a collection of efficient neural network kernels developed to maximize the performance and minize the memory footprint of neural networks on Cortex-M processor. 