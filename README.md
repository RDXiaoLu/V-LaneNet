# V-LaneNet


OLNet achieves an F1 score of **81.17** on **CULane** while running at **71 FPS**, offering a practical solution for robust lane detection in the wild.


---

## Highlights
- **Robust under occlusion & sparse markings** via realistic *Shadow Lane Augmentation (SLA)*
- **Improved local details + global semantics** with *Multi-Domain FPN* and a *Dual-Domain Fourier Contextual Mixer*
- **Better geometry and direction inference** using a *Direction-Aware IoU loss (LO-IoU)*
- **Strong accuracy–speed trade-off**: CULane **F1 = 81.17**, **71 FPS** with frozen backbones and lightweight heads
- **Modular, easy to integrate** into existing lane detection pipelines

---


---

## Results on CULane
- **F1**: 81.17  
- **Speed**: 71 FPS (single GPU, 1920×1080 input; details below)  
- Strong performance on scenarios with occlusion and low visibility.

> Exact speed depends on GPU, input size, and I/O. Reported FPS measured on an NVIDIA-class GPU with mixed precision enabled.

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

### SLA Occlusion Data Generation
SLA requires SAM and LaMa:

- SAM weights and code: https://github.com/facebookresearch/segment-anything  
- LaMa inpainting: https://github.com/advimman/lama

We provide a script to generate occlusion/missing-mark variants:
```bash
# Example: generate SLA-augmented training set
python tools/generate_sla.py \
  --input data/CULane \
  --output data/CULane_SLA \
  --sam-checkpoint checkpoints/sam_vit_h.pth \
  --lama-config third_party/lama/configs/prediction/default.yaml \
  --lama-checkpoint checkpoints/big-lama.pt \
  --occlude-prob 0.4 \
  --erase-prob 0.3 \
  --shadow-prob 0.3 \
  --max-objects 5
```
- `--occlude-prob`: paste SAM masks as occluders
- `--erase-prob`: LaMa removes lane fragments to simulate wear/missing
- `--shadow-prob`: optional synthetic shadow overlay
- The script writes images and masks with the same naming pattern under `data/CULane_SLA`.

Update your config to use `CULane_SLA` for training.

---

## Training
We provide a default config for CULane:
```bash
# Single GPU
python tools/train.py --config configs/olnet_culane.yaml

# Multi-GPU (DDP)
torchrun --nproc_per_node 8 tools/train.py --config configs/olnet_culane.yaml

# Common options
#   --amp                # enable mixed precision
#   --seed 42            # reproducibility
#   --resume checkpoint.pth
```

Key config fields (see `configs/olnet_culane.yaml`):
- `model.backbone`: e.g., `resnet50` or `convnext_tiny` (frozen if desired)
- `model.mdfpn.ddfcm`: enable Dual-Domain Fourier Contextual Mixer
- `loss.lo_iou.lambda`: direction weight \(\lambda\) (default 0.5)
- `data.train.root`: set to `data/CULane_SLA` to include SLA
- `train.img_size`: e.g., 360×640 or 720p/1080p
- `optim`: AdamW with cosine decay, warmup; default `lr=4e-4`, `wd=0.05`, `epochs=12–24`
- `batch_size`: 8–32 depending on GPU RAM

---

## Evaluation
```bash
python tools/eval.py \
  --config configs/olnet_culane.yaml \
  --checkpoint checkpoints/olnet_culane_f1_81p17.pth \
  --split test
```
Outputs standard CULane metrics and per-scenario breakdown.

---




---

## Model Zoo
| Model | Dataset | F1 | FPS | Checkpoint |
|------|---------|----|-----|------------|
| OLNet (SLA + MDFPN + LO-IoU) | CULane | 81.17 | 71 | coming soon |

> We will release more backbones and training recipes.

---

## Roadmap
- [ ] Release checkpoints and occlusion data
- [ ] Add TensorRT export and INT8 quantization
- [ ] Add TuSimple/LLAMAS evaluations
- [ ] Support multilingual logs and docs
- [ ] Provide Docker image

---

## FAQ
- **Q: Do I need SLA to train?**  
  A: Not strictly, but SLA significantly improves robustness under occlusion/missing markings.

- **Q: Is LO-IoU compatible with polyline-based heads?**  
  A: Yes. Rasterize polylines to thin tubes for IoU, and estimate \(\theta\) from local tangents.


---



## Acknowledgments
- **CULane** dataset: Pan et al.  
- **SAM**: Kirillov et al., “Segment Anything”  
- **LaMa**: Suvorov et al., “LaMa: Resolution-robust Large Mask Inpainting with Fourier Convolutions”

We thank the authors and the open-source community for their contributions.

---

## Contact
- Maintainer: Your Name (`your.email@domain.com`)
- Issues and feature requests: please open a GitHub Issue

