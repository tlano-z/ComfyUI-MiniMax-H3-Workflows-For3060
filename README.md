# ComfyUI-MiniMax-H3-Workflows-For3060

RTX 3060 12GB環境でMiniMax-H3をローカル実行するための、T2V（Text to Video）／I2V（Image to Video）用ComfyUIワークフローです。

## 収録ワークフロー

- `MiniMax-H3_T2V.json` — Text to Video
- `MiniMax-H3_I2V.json` — Image to Video
- `MiniMax-H3_T2V_TAE-Preview.json` — T2V＋TAEライブプレビュー
- `MiniMax-H3_I2V_TAE-Preview.json` — I2V＋TAEライブプレビュー

## ハードウェア・実行環境

- **GPU:** NVIDIA GeForce RTX 3060 12GB
- **RAM:** 64GB
- **GPUドライバー:** 591.86
- **ComfyUI:** 0.30.0（[PR #15334](https://github.com/Comfy-Org/ComfyUI/pull/15334) のMiniMax-H3 int8 ConvRot Video VAE対応を含むビルド）
- **Python:** 3.10.10
- **PyTorch:** 2.11.0+cu130
- **CUDA Runtime:** 13.0
- **cuDNN:** 9.19
- **SageAttention:** 2.2.0
- **Triton Windows:** 3.7.1.post27

## 使用モデル

### MiniMax-H3本体

`minimax_h3_fl2va_pruned_int8_convrot.safetensors`  
約20.97GB

### テキストエンコーダー

`qwen3vl_32b_minimax_h3_nvfp4_awq.safetensors`  
約15.69GB

### Video VAE

`minimax_h3_video_vae_int8_convrot.safetensors`

この量子化Video VAEを使用するには、[ComfyUI PR #15334](https://github.com/Comfy-Org/ComfyUI/pull/15334) の変更を含むComfyUIが必要です。

配布元：  
[MiniMax-H3 experimental — int8 ConvRot Video VAE](https://huggingface.co/Kijai/MiniMax-H3-experimental/blob/main/minimax_h3_video_vae_int8_convrot.safetensors)

配置先：

```text
ComfyUI/models/vae/minimax_h3_video_vae_int8_convrot.safetensors
```

### Audio VAE

`minimax_h3_audio_vae_fp32.safetensors`  
約0.61GB

モデル群はRTX 3060の12GB VRAMには収まらないため、ComfyUIのDynamic VRAMとCPUオフロードを使用します。

## 生成設定

- **Sampling steps:** 25
- **EasyCache reuse_threshold:** 0.300
- **EasyCache start_percent:** 0.200
- **EasyCache end_percent:** 0.900
- **SageAttention:** 有効
- **Dynamic VRAM／CPUオフロード:** 有効
- **CUDAモジュール遅延ロード:** 有効
- **生成中プレビュー:** 通常版では無効

## TAEプレビュー版の追加要件

`TAE-Preview`付きワークフローでは、以下を追加で使用します。

- **カスタムノード:** ComfyUI-KJNodes（`ModelPreviewOverrideKJ`）
- **TAEモデル:** `taeh3.safetensors`
- **プレビュー設定:** 最大512px、JPEG品質80、12フレーム、12fps

TAEは生成中のプレビューにのみ使用し、最終動画のデコードには`minimax_h3_video_vae_int8_convrot.safetensors`を使用します。
