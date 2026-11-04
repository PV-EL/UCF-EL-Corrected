# UCF-EL-Corrected

Corrected pixel-level annotations for a subset of the [UCF EL Defect dataset](https://github.com/ucf-photovoltaics/UCF-EL-Defect).

This repository releases the annotation file produced by a manual audit of the public UCF EL Defect
benchmark. The audit examined 730 stratified sampled cell images, identified 176 images with label
problems (24.11%), and corrected 114 of them (0.96% of the 11,851 defect-bearing images). The
corrected labels are distributed as a single CSV exported from the annotation software, in the same
format as the original `annotations.csv`, so that it can be used as a drop-in replacement.

The audit and the corrections are described in the accompanying paper.

---

## 1. What is included

```
UCF-EL-Corrected.csv      corrected annotations (VIA format, polygons), 114 images
changes.csv               per-image change log: image name, change type, short note
figures/                  example panels used in this README
README.md                 this file
```

Images are **not** redistributed here. Download the original `Test_Images/` from the UCF EL Defect
repository and match by filename.

Label convention (identical to the original dataset):

| id | class | name used in this repository |
|----|-------|------------------------------|
| 0 | background / No-Defect | — |
| 1 | Crack | Crack |
| 2 | Contact | Contact |
| 3 | Corrosion | Corrosion |
| 4 | Interconnect-HR | HRI (Highly Resistive Interconnects) |
| 5 | Interconnect-BS | BI (Bright Interconnects) |

### What the corrections look like

![Examples of the four label-problem types](figures/annotation_examples.png)

Four representative images, one per problem type (left to right: original EL image, original
annotation, corrected annotation). The red box marks the region under discussion; masks are
overlaid at 25% opacity so the underlying image stays visible.

| row | problem type | what was changed |
|-----|--------------|------------------|
| 1 | missed annotation | the interconnect segment had no mask; it is now annotated as HRI |
| 2 | wrong class | the region was labelled BI although its grey level and position indicate HRI |
| 3 | inconsistent boundary | the mask covered only part of the bright segment; its extent was redrawn |
| 4 | false positive | the region was annotated as a defect but contains no defect; the mask was removed |

These four types are the audit taxonomy used throughout; the counts per class are given in the
accompanying paper.

---

## 2. How to use

The CSV is exported straight from the annotation software (VGG Image Annotator, VIA) and can be
opened with VIA directly

Notes:

- Polygons are stored in the coordinate frame of the **original** image resolution; resize after
  rasterising, or scale the polygon coordinates first, if you evaluate at 256×256.
- Pixels covered by no polygon are background (label 0), which includes both "no defect" and
  "not annotated". The distinction matters: see §4.
- The 27 images converted to No-Defect have an **empty** annotation in this file. They are kept in
  the file so that the two dataset versions share the same image list and the same train/test split.

---

## 3. How the annotations were produced

**Tool.** All corrections were made in [VGG Image Annotator (VIA)](https://www.robots.ox.ac.uk/~vgg/software/via/)
running locally in a browser. Regions are drawn as polygons, and the file released here is the
unaltered VIA export, so region geometry and class attributes can be recovered without conversion.
As with the original dataset, the CSV is assembled from annotation sessions: the exported regions
were merged per image and the class ids were re-checked after merging.

**Procedure.** The images were reviewed one by one by the first author. For a questionable region,
the decision combined four cues — defect morphology, grey level, spatial position, and the relation
to the neighbouring interconnect structure — under a single class-definition rule set derived from
the original annotation guide. Regions whose class was unambiguous were re-labelled; regions whose
extent was clearly wrong were redrawn; regions that could not be judged from the image alone were
left as they were rather than guessed.

**Controls.** No model output was displayed during the audit, so no prediction bias could leak into
the labels. Two weeks after the corrections, 160 of the 730 audited images (80 of them corrected)
were re-read blind to the previous reading and to model output; the second reading agreed with the
first on the class of the audited region in essentially all cases.

**Scope.** Only the two interconnect classes were corrected, because their problems are dominated by
defect/background confusion and class confusion — disagreements about the *definition* of a class,
which a single rule set can settle consistently. In 27 of the 114 images the corrected mask is empty
(the earlier annotation was a false positive). Crack, Contact and Corrosion were left untouched: their
errors are mostly boundary shifts, which are far more dependent on the individual annotator and are
not addressed by this release.

---

## 4. Limitations and disclaimer

**Please read this section before using the labels.**

- **The corrections reflect one annotator's judgement.** All 114 corrected masks were produced by a
  single person, working from a written rule set. No second independent annotator was involved and
  no inter-annotator agreement is reported. A different expert, applying the same guide, could have
  drawn some boundaries differently or kept some regions unchanged.
- **This is not a gold standard.** The release is a *partial, consistency-oriented* revision of the
  original labels, not a re-annotation of the dataset. Of the 11,851 defect-bearing images, 0.96%
  were corrected; the remaining labels — including residual errors — are the original ones.
- **Class counts are not additive.** Because one image can contain several defect classes, the
  per-class image counts in the accompanying paper overlap and do not sum to the number of images
  in the file. Do not use them as class frequencies.
- **The corrected labels are consistent with the original class definitions, not with a new
  taxonomy.** They should not be used to argue that a model is better or worse than published
  numbers obtained on the original labels; re-evaluate all methods on the same version if you want a
  comparable ranking.
- **Coverage is limited.** The audit sampled 730 images (6.16%) and the corrections touch a subset of
  them; module types, imaging conditions and defect populations outside the original dataset are not
  represented. Generalisation to other EL datasets is not established.
- **No warranty.** The annotations are provided for research use, "as is", without warranty of any
  kind. The authors accept no liability for decisions made on the basis of these labels. If you find
  an error, please open an issue — corrections are welcome and will be versioned.

---

## 5. 中文说明

- 本仓库发布的是对公开 UCF EL Defect 数据集做人工审计后的**修正标注**（CSV，VIA 导出格式），
  覆盖 114 张图像，占 11,851 张含缺陷图像的 0.96%。
- 标注在 VIA 中完成，导出后按图像合并各次标注会话；修正过程中不显示任何模型输出，全程人工判读。
- 修正只涉及两类互连缺陷（HRI、BI）与转判为 No-Defect 的 27 张图像；Crack、Contact、Corrosion
  未作改动。
- **免责声明**：全部修正由作者一人依据统一准则完成，未经第二标注者独立复核，因此带有个人经验
  带来的判断偏差；本版本是**部分修正版**而非金标准，未修正图像的原始标注噪声仍然存在。使用本
  标注得到的结果，其解释范围应限于本文所声明的协议之内。

