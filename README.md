# Running GraphCast on Lengau

This guide outlines the steps to set up and run **GraphCast** on the Lengau High-Performance Computing (HPC) system. GraphCast is a machine learning model for weather forecasting developed by DeepMind.

---

## 1. Prerequisites

Before proceeding, ensure you have:

1. **Access to Lengau**: An active HPC account with required permissions.
2. **Datasets**: Weather data in the format expected by GraphCast (e.g., ERA5 reanalysis datasets).
3. **Required Software**:
   - Python (version 3.8 or later recommended).
   - TensorFlow or PyTorch (specific version depends on GraphCast).
   - Other Python dependencies listed in the GraphCast repository.

---

## 2. Set Up the Environment

1. **Load Required Modules**:
   Use `module avail` to check available software on Lengau, and load the necessary modules:
   ```bash
   module load python/3.8
   module load tensorflow/2.12
   ```

2. **Create a Python Virtual Environment**:
   Setting up a virtual environment ensures dependency isolation:
   ```bash
   python -m venv graphcast_env
   source graphcast_env/bin/activate
   ```

3. **Install Dependencies**:
   Clone the GraphCast repository (if publicly available) or download it:
   ```bash
   git clone <GraphCast-repository-URL>
   cd GraphCast
   ```
   Install the required Python packages:
   ```bash
   pip install -r requirements.txt
   ```

4. **Set Up GPU Support**:
   Load GPU modules for efficient computation:
   ```bash
   module load cuda/11.8
   module load cudnn/8.6
   ```
   Verify TensorFlow detects GPUs:
   ```bash
   python -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"
   ```

---

## 3. Prepare Input Data

1. **Data Format**:
   Ensure input data is in the format required by GraphCast, such as preprocessed gridded weather data (e.g., NetCDF or HDF5). Refer to the GraphCast documentation for specific details.

2. **Data Location**:
   Place your datasets in a Lustre filesystem directory for better performance:
   ```bash
   /mnt/lustre/users/<username>/graphcast_data/
   ```

---

## 4. Run GraphCast

### Prepare the Configuration
Edit the configuration file (e.g., `config.yaml`) to specify:
- Input data paths.
- Model parameters.
- Output directories.

### Submit a Batch Job
Create a Slurm job script named `graphcast_job.slurm` to run GraphCast on Lengau:

```bash
#!/bin/bash
#SBATCH --job-name=graphcast_run
#SBATCH --partition=gpu
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8
#SBATCH --gres=gpu:1
#SBATCH --time=02:00:00
#SBATCH --output=graphcast_%j.log

module load python/3.8
module load tensorflow/2.12
module load cuda/11.8
module load cudnn/8.6

source /path/to/graphcast_env/bin/activate

python run_graphcast.py --config config.yaml
```

Submit the job using:
```bash
sbatch graphcast_job.slurm
```

### Monitor the Job
Check job status with:
```bash
squeue -u <your-username>
```
Monitor output using:
```bash
tail -f graphcast_<job_id>.log
```

---

## 5. Post-Processing

After the model run is complete:
1. Check the output directory for results.
2. Use Python scripts or visualization tools (e.g., Matplotlib) for analysis.

---

## Notes

- Refer to GraphCast documentation for any special requirements or configuration details.
- The GPU queue on Lengau may have limited slots. Test your code on the development queue before scaling to production runs.

---

Let us know if you need further assistance!

