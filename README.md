# Training Models on Google-Cloud
This describes necessary steps to train models on Google Cloud, assuming you are familiar with Google Cloud console and installed Google Cloud SDK

### Enable following APIs in Google Cloud console

* Compute
* Billing
* Storage
* ML Engine


#### Upload your datasets faster using parallel transfer
> Rename bucket name with your own wherever it says **zubair-gc-bucket**

```python
gsutil -m cp -r datasets gs://zubair-gc-bucket/datasets
```
![image](https://user-images.githubusercontent.com/1317442/37080569-92661eca-2208-11e8-8c5d-c89853e3e73d.png)

<dl>
  <dt> Create a **trainer** folder and move all your project files in it</dt>  
</dl>

* Create empty __init__.py in a **trainer** folder under your project
* Add **Setup.py** outside **trainer** folder

![image](https://user-images.githubusercontent.com/1317442/37085387-bd9fad28-2216-11e8-8022-028275fa130c.png)

#### Open **Setup.py** and add following

```python
'''Cloud ML Engine package configuration.'''
from setuptools import setup, find_packages

setup(name='shallownet_keras',
      version='1.0',
      packages=find_packages(),
      include_package_data=True,
      description='Model Training using Keras on Google Cloud',
      author='Zubair',
      author_email='your email',
      license='MIT',
      install_requires=[
          'keras',
          'h5py'],
      zip_safe=False)
```

#### Add a new argument in your model code before parsing

```python

add.add_argument(
      '--job-dir',
      help='Cloud storage bucket to export the model and store temp files')

args = vars(ap.parse_args())

```

#### Include following for saving your model on Google cloud

```python
if args["--job-dir"] != '':
	with file_io.FileIO(args["model"], mode='r') as input_f:
		with file_io.FileIO(args["--job-dir"] + '/' + args["model"], mode='w+') as output_f:
		  output_f.write(input_f.read())

```

#### Issue following command
> On windows cmd doesn't work with multiline text

```
gcloud ml-engine jobs submit training job2 --package-path=./trainer --module-name=trainer/shallownet_train --job-dir=gs://zubair-gc-bucket/jobs/job2 --region=us-central1 --config=trainer/cloudml-gpu.yaml --runtime-version="1.4" -- --job_name="zubair-gc-job2"  --dataset=dataset/animals --model=shallownet_weights1.hdf5
```

#### Job starts running on Google Cloud
![image](https://user-images.githubusercontent.com/1317442/37086691-0796fdf2-221a-11e8-90b3-a57564156886.png)
