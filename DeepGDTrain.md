## Training DeepGD on the Research Cluster
This guide covers my current steps for training DeepGD on the Research Cluster.

1. **Create an Environment and Clone the Code**  
   First, request a compute node with a GPU and load Anaconda and CUDA 11.8:
   ```bash
   srun --partition=gpu --nodes=1 --gres=gpu:v100-sxm2:1 --cpus-per-task=2 --mem=10GB --time=02:00:00 --pty /bin/bash
   module load anaconda3/2022.05 cuda/11.8    
   ```
   
   Next, create a Conda environment in your scratch or work directory. In my case, I used `/scratch/li.xuefen/gdtest`:
   ```bash
   conda create --prefix=/scratch/li.xuefen/gdtest -c conda-forge python=3.10 -y
   conda activate /scratch/li.xuefen/gdtest
   ```

   Then, navigate to the scratch directory and clone the DeepGD repository:
   ```bash
   cd /scratch/li.xuefen/
   git clone https://github.com/yolandalalala/DeepGD.git
   cd DeepGD
   ```

2. **Install Dependencies**  
   Install the required dependencies:
   ```bash
   pip install -r requirements.txt
   ```

   Next, install PyTorch, TorchVision, and Torchaudio with CUDA 11.8 compatibility:
   ```bash
   pip install torch==2.0.0+cu118 torchvision==0.15.0+cu118 torchaudio==2.0.0+cu118 --extra-index-url https://download.pytorch.org/whl/cu118
   ```

   Additionally, install any other necessary packages:
   ```bash
   pip install pandas matplotlib attrs ssgetpy
   ```

5. **Making DeepGD a Module**  
   To ensure DeepGD is recognized as a module, add an `__init__.py` file:
   ```bash
   touch deepgd/__init__.py
   ```

6. **Finally Train DeepGD**
