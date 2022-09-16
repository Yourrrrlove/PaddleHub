# disco_diffusion_cnclip_vitb16

|Module Name|disco_diffusion_cnclip_vitb16|
| :--- | :---: |
|Category|text to image|
|Network|dd+cnclip ViTB16|
|Dataset|-|
|Fine-tuning supported or not|No|
|Module Size|2.9GB|
|Latest update date|2022-08-02|
|Data indicators|-|

## I.Basic Information

### Application Effect Display

   - Prompt "在宁静的风景中画一幅美丽的建筑画，由Arthur Adams在artstation上所作"

   - Output image
   <p align="center">
     <img src="https://user-images.githubusercontent.com/22424850/184838000-566f0548-f0f5-4df6-a4af-16bb70220137.png"  width = "80%" hspace='10'/>
   <br />

   - Generating process
   <p align="center">
     <img src="https://user-images.githubusercontent.com/22424850/184837520-f30f21e1-5310-4925-a8d2-946337795010.gif"  width = "80%" hspace='10'/>
   <br />

### Module Introduction

disco_diffusion_cnclip_vitb16 is a text-to-image generation model that can generate images that match the semantics of the sentence you prompt. The model consists of two parts, one is the diffusion model, which is a generative model that reconstructs the original image from the noisy input. The other part is the multimodal pre-training model (CLIP), which can represent text and images in the same feature space, and text and images with similar semantics will be closer in this feature space. In the text image generation model, the diffusion model is responsible for generating the target image from the initial noise or the specified initial image, and CLIP is responsible for guiding the generated image to be as close as possible to the semantics of the input text. Diffusion model under the guidance of CLIP iteratively generates new images, eventually generating images of what the text describes. The CLIP model used in this module is ViTB16.

For more details, please refer to [Diffusion Models Beat GANs on Image Synthesis](https://arxiv.org/abs/2105.05233) and [Learning Transferable Visual Models From Natural Language Supervision](https://arxiv.org/abs/2103.00020)

## II.Installation

- ### 1.Environmental Dependence

  - paddlepaddle >= 2.0.0

  - paddlehub >= 2.2.0    | [How to install PaddleHub](../../../../docs/docs_en/get_start/installation.rst)

- ### 2.Installation

  - ```shell
    $ hub install disco_diffusion_cnclip_vitb16
    ```
  - In case of any problems during installation, please refer to:[Windows_Quickstart](../../../../docs/docs_en/get_start/windows_quickstart.md) | [Linux_Quickstart](../../../../docs/docs_en/get_start/linux_quickstart.md) | [Mac_Quickstart](../../../../docs/docs_en/get_start/mac_quickstart.md)


## III.Module API Prediction  

- ### 1.Command line Prediction

  - ```shell
    $ hub run disco_diffusion_cnclip_vitb16 --text_prompts "孤舟蓑笠翁，独钓寒江雪。风格如齐白石所作。" --output_dir disco_diffusion_cnclip_vitb16_out
    ```

- ### 2.Prediction Code Example

  - ```python
    import paddlehub as hub

    module = hub.Module(name="disco_diffusion_cnclip_vitb16")
    text_prompts = ["孤舟蓑笠翁，独钓寒江雪。"]
    # Output images will be saved in disco_diffusion_cnclip_vitb16_out directory.
    # The returned da is a DocumentArray object, which contains all immediate and final results
    # You can manipulate the DocumentArray object to do post-processing and save images
    da = module.generate_image(text_prompts=text_prompts, artist='齐白石', output_dir='./disco_diffusion_cnclip_vitb16_out/')  
    # Save final result image to a file
    da[0].save_uri_to_file('disco_diffusion_cnclip_vitb16_out-result.png')
    # Show all immediate results
    da[0].chunks.plot_image_sprites(skip_empty=True, show_index=True, keep_aspect_ratio=True)
    # Save the generating process as a gif
    da[0].chunks.save_gif('disco_diffusion_cnclip_vitb16_out-result.gif')
    ```

- ### 3.API

  - ```python
    def generate_image(
            text_prompts,
            style: Optional[str] = None,
            artist: Optional[str] = None,
            width_height: Optional[List[int]] = [1280, 768],
            seed: Optional[int] = None,
            output_dir: Optional[str] = 'disco_diffusion_cnclip_vitb16_out'):
    ```

    - Image generating api, which generates an image corresponding to your prompt.

    - **Parameters**

      - text_prompts(str): Prompt, used to describe your image content. You can construct a prompt conforms to the format "content" + "artist/style", such as "孤舟蓑笠翁，独钓寒江雪。风格如齐白石所作". For more details, you can refer to [website](https://docs.google.com/document/d/1XUT2G9LmkZataHFzmuOtRXnuWBfhvXDAo8DkS--8tec/edit#).
      - style(Optional[str]): Image style, such as "watercolor" and "Chinese painting". If not provided, style is totally up to your prompt.
      - artist(Optional[str]): Artist name, such as 齐白石,Greg Rutkowsk，image style is as whose works you choose. If not provided, style is totally up to your [prompt](https://weirdwonderfulai.art/resources/disco-diffusion-70-plus-artist-studies/).
      - width_height(Optional[List[int]]): The width and height of output images, should be better multiples of 64. The larger size is, the longger computation time is.
      - seed(Optional[int]): Random seed, different seeds result in different output images.
      - output_dir(Optional[str]): Output directory, default is "disco_diffusion_cnclip_vitb16_out".


    - **Return**
      - ra(DocumentArray): DocumentArray object， including `n_batches` Documents，each document keeps all immediate results during generation, please refer to [DocumentArray tutorial](https://docarray.jina.ai/fundamentals/documentarray/index.html) for more details.

## IV.Server Deployment

- PaddleHub Serving can deploy an online service of text-to-image.

- ### Step 1: Start PaddleHub Serving

  - Run the startup command：
  - ```shell
    $ hub serving start -m disco_diffusion_cnclip_vitb16
    ```

  - The servitization API is now deployed and the default port number is 8866.

  - **NOTE:**  If GPU is used for prediction, set CUDA_VISIBLE_DEVICES environment variable before the service, otherwise it need not be set.

- ### Step 2: Send a predictive request

  - With a configured server, use the following lines of code to send the prediction request and obtain the result.

  - ```python
    import requests
    import json
    import cv2
    import base64
    from docarray import DocumentArray

    # Send an HTTP request
    data = {'text_prompts': '孤舟蓑笠翁，独钓寒江雪。风格如齐白石所作'}
    headers = {"Content-type": "application/json"}
    url = "http://127.0.0.1:8866/predict/disco_diffusion_cnclip_vitb16"
    r = requests.post(url=url, headers=headers, data=json.dumps(data))

    # Get results
    da = DocumentArray.from_base64(r.json()["results"])
    # Save final result image to a file
    da[0].save_uri_to_file('disco_diffusion_cnclip_vitb16_out-result.png')
    # Save the generating process as a gif
    da[0].chunks.save_gif('disco_diffusion_cnclip_vitb16_out-result.gif')


## V.Release Note

* 1.0.0

  First release

  ```shell
  $ hub install disco_diffusion_cnclip_vitb16 == 1.0.0
  ```