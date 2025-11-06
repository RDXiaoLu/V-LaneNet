# V-LaneNet


V-LaneNet achieves an F1 score of **81.17** on **CULane** while running at **71 FPS**, offering a practical solution for robust lane detection in the wild.




---




## Getting Started



### Dataset Preparation (CULane)
- Download CULane from the official site: https://xingangpan.github.io/projects/CULane.html
- Expected structure:
```
data/
  CULane/
    driver_100_30frame/
    driver_161_90frame/
    ...
    list/
      test.txt
      train.txt
      val.txt
```
- Update `data_root` in `configs/olnet_culane.yaml` accordingly.

### V-LaneAug Data Generation
V-LaneAug requires SAM and LaMa:

- SAM weights and code: https://github.com/facebookresearch/segment-anything  
- LaMa inpainting: https://github.com/advimman/lama

We provide a script to generate occlusion/missing-mark variants:


---






---

## Model Zoo
| Model | Dataset | F1 | FPS | Checkpoint |
|------|---------|----|-----|------------|
| V-LaneNet (V-LaneAug + V-LaneMixer + V-LaneIoU) | CULane | 81.17 | 71 | coming soon |

> We will release more backbones and training recipes.

---

## Roadmap
- [ ] Release checkpoints and occlusion data
- [ ] Add TensorRT export and INT8 quantization
- [ ] Support multilingual logs and docs
- [ ] Provide Docker image




---

## Acknowledgments
- **CULane** dataset: Pan et al.  
- **SAM**: Kirillov et al., “Segment Anything”  
- **LaMa**: Suvorov et al., “LaMa: Resolution-robust Large Mask Inpainting with Fourier Convolutions”

We thank the authors and the open-source community for their contributions.

---

## Contact
- Maintainer: luzhaoxuan@smail.fjut.edu.cn
- Issues and feature requests: please open a GitHub Issue

