This repository contains source code for the experiments in a [paper](http://arxiv.org/abs/1507.02672) titled
_Semi-Supervised Learning with Ladder Network_ by A Rasmus, H Valpola, M Honkala,
M Berglund, and T Raiko.

#### Required libraries
##### Install Theano, Blocks Stable 0.0.1, Fuel Stable 0.0.1
Refer to [Blocks installation instructions](http://blocks.readthedocs.org/en/latest/setup.html) for
details but use tag v0.0.1 instead. Something along:
```
pip install git+git://github.com/mila-udem/blocks.git@v0.0.1
pip install git+git://github.com/mila-udem/fuel.git@v0.0.1
```
Fuel comes with the Blocks, but you need to download and convert the datasets.
Refer to Fuel documentation.
```
fuel-download mnist
fuel-convert mnist --dtype float32
fuel-download cifar10
fuel-convert cifar10
```

#### Models in the paper

The following commands train the models with seed 1. The reported numbers in the paper are averages over
several random seeds. These commands use all the training samples for training (`--unlabeled-samples 60000`)
and none is used for validation. This results in a lot of NaNs being printed during the trainining, since
validation statistics are not available. If you want to observe the validation error and costs during the training,
use `--unlabeled-samples 50000`.

##### Evaluating models with testset
After training a model, you can infer the results on a test set by performing the `evaluate` command.
An example use after training a model:
```
./run.py evaluate results/mnist_all_bottom0
```

##### MNIST all labels
```
# Bottom (better than the Full models we tested)
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec sig --denoising-cost-x 500,0,0,0,0,0,0 --labeled-samples 60000 --unlabeled-samples 60000 --seed 1 -- mnist_all_bottom
# Gamma model
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec 0-0-0-0-0-0-sig --denoising-cost-x 0,0,0,0,0,0,1 --labeled-samples 60000 --unlabeled-samples 60000 --seed 1 -- mnist_all_gamma
# Supervised baseline
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec 0-0-0-0-0-0-0 --denoising-cost-x 0,0,0,0,0,0,0 --labeled-samples 60000 --unlabeled-samples 60000 --f-local-noise-std 0.5 --seed 1 -- mnist_all_baseline
```

##### MNIST 100 labels
```
# Full
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec sig --denoising-cost-x 1000,10,0.1,0.1,0.1,0.1,0.1 --labeled-samples 100 --unlabeled-samples 60000 --seed 1 -- mnist_100_full
# Bottom-only
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec sig --denoising-cost-x 4000,0,0,0,0,0,0 --labeled-samples 100 --unlabeled-samples 60000 --seed 1 -- mnist_100_bottom
# Gamma
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec 0-0-0-0-0-0-sig --denoising-cost-x 0,0,0,0,0,0,1 --labeled-samples 100 --unlabeled-samples 60000 --seed 1 -- mnist_100_gamma
# Supervised baseline
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec 0-0-0-0-0-0-0 --denoising-cost-x 0,0,0,0,0,0,0 --labeled-samples 100 --unlabeled-samples 60000 --f-local-noise-std 0.5 --seed 1 -- mnist_100_baseline
```

##### MNIST 1000 labels
```
# Full
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec sig --denoising-cost-x 1000,10,0.1,0.1,0.1,0.1,0.1 --f-local-noise-std 0.2 --labeled-samples 1000 --unlabeled-samples 60000 --seed 1 -- mnist_1000_full
# Bottom-only
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec sig --denoising-cost-x 1000,0,0,0,0,0,0 --labeled-samples 1000 --unlabeled-samples 60000 --seed 1 -- mnist_1000_bottom
# Gamma model
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec 0-0-0-0-0-0-sig --denoising-cost-x 0,0,0,0,0,0,5 --labeled-samples 1000 --unlabeled-samples 60000 --seed 1 -- mnist_1000_gamma
# Supervised baseline
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec 0-0-0-0-0-0-0 --denoising-cost-x 0,0,0,0,0,0,0 --labeled-samples 1000 --unlabeled-samples 60000 --f-local-noise-std 0.5 --seed 1 -- mnist_1000_baseline
```

##### MNIST 50 labels
```
# Full model
run.py train --encoder-layers 1000-500-250-250-250-10 --decoder-spec sig --denoising-cost-x 2000,20,0.01,0.01,0.01,0.01,0.05 --labeled-samples 50 --unlabeled-samples 60000 --seed 1 -- mnist_50_full
```

##### MNIST convolutional models
```
# Conv-FC
run.py train --encoder-layers convv:1000:26:1:1-convv:500:1:1:1-convv:250:1:1:1-convv:250:1:1:1-convv:250:1:1:1-convv:10:1:1:1-globalmeanpool:0 --decoder-spec sig --denoising-cost-x 1000,10,0.1,0.1,0.1,0.1,0.1,0.1 --labeled-samples 100 --unlabeled-samples 60000 --seed 1 -- mnist_100_conv_fc
# Conv-Small, Gamma
run.py train --encoder-layers convf:32:5:1:1-maxpool:2:2-convv:64:3:1:1-convf:64:3:1:1-maxpool:2:2-convv:128:3:1:1-convv:10:1:1:1-globalmeanpool:6:6-fc:10 --decoder-spec 0-0-0-0-0-0-0-0-0-sig --denoising-cost-x 0,0,0,0,0,0,0,0,0,1 --labeled-samples 100 --unlabeled-samples 60000 --seed 1  -- mnist_100_conv_gamma
# Conv-Small, supervised baseline. Overfits easily, so keep training short.
run.py train --encoder-layers convf:32:5:1:1-maxpool:2:2-convv:64:3:1:1-convf:64:3:1:1-maxpool:2:2-convv:128:3:1:1-convv:10:1:1:1-globalmeanpool:6:6-fc:10 --decoder-spec 0-0-0-0-0-0-0-0-0-0 --denoising-cost-x 0,0,0,0,0,0,0,0,0,0 --num-epochs 20 --lrate-decay 0.5 --f-local-noise-std 0.45 --labeled-samples 100 --unlabeled-samples 60000 --seed 1 -- mnist_100_conv_baseline
```

##### CIFAR models
```
# Conv-Large, Gamma
./run.py train --encoder-layers convv:96:3:1:1-convf:96:3:1:1-convf:96:3:1:1-maxpool:2:2-convv:192:3:1:1-convf:192:3:1:1-convv:192:3:1:1-maxpool:2:2-convv:192:3:1:1-convv:192:1:1:1-convv:10:1:1:1-globalmeanpool:0 --decoder-spec 0-0-0-0-0-0-0-0-0-0-0-0-sig --dataset cifar10 --act leakyrelu --denoising-cost-x 0,0,0,0,0,0,0,0,0,0,0,0,4.0 --num-epochs 70 --lrate-decay 0.86 --seed 1 --whiten-zca 3072 --contrast-norm 55 --top-c False --labeled-samples 4000 --unlabeled-samples 50000 -- cifar_4k_gamma
# Conv-Large, supervised baseline. Overfits easily, so keep training short.
./run.py train --encoder-layers convv:96:3:1:1-convf:96:3:1:1-convf:96:3:1:1-maxpool:2:2-convv:192:3:1:1-convf:192:3:1:1-convv:192:3:1:1-maxpool:2:2-convv:192:3:1:1-convv:192:1:1:1-convv:10:1:1:1-globalmeanpool:0 --decoder-spec 0-0-0-0-0-0-0-0-0-0-0-0-0 --dataset cifar10 --act leakyrelu --denoising-cost-x 0,0,0,0,0,0,0,0,0,0,0,0,0 --num-epochs 20 --lrate-decay 0.5 --seed 1 --whiten-zca 3072 --contrast-norm 55 --top-c False --labeled-samples 4000 --unlabeled-samples 50000 -- cifar_4k_baseline
```
