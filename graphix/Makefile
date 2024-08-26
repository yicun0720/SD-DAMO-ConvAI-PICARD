BASE_DIR := $(shell pwd)

.PHONY: pull-train-image
pull-train-image:
        # docker pull tscholak/$(TRAIN_IMAGE_NAME):$(GIT_HEAD_REF)
        docker pull eyuansu62/graphix-text-to-sql:v2


.PHONY: pull-eval-image
pull-eval-image:
        # docker pull tscholak/$(EVAL_IMAGE_NAME):$(GIT_HEAD_REF)
        docker pull eyuansu62/graphix-text-to-sql:v2

.PHONY: train
train: 
	mkdir -p -m 777 train_db_id_$(SUFFIX_DATA)
	mkdir -p -m 777 transformers_cache_$(SUFFIX_DATA)
	mkdir -p -m 777 wandb
	- chmod 777 data_all_in_$(SUFFIX_DATA)/*
	docker run \
			-it \
			--rm \
			--gpus '"device=$(AVAILABLE_GPUS)"'\
			--user 13011:13011 \
			-v $(BASE_DIR)/docker_patches/.bashrc:/home/toolkit/.bashrc \
			-v $(BASE_DIR)/docker_patches/.ssh:/home/toolkit/.ssh \
			--mount type=bind,source=$(BASE_DIR)/train_db_id_$(SUFFIX_DATA),target=/train_db_id \
			--mount type=bind,source=$(BASE_DIR)/transformers_cache_$(SUFFIX_DATA),target=/transformers_cache \
			--mount type=bind,source=$(BASE_DIR)/configs,target=/app/configs \
			--mount type=bind,source=$(BASE_DIR)/configs/train.json,target=/app/configs/train.json \
			--mount type=bind,source=$(BASE_DIR)/seq2seq,target=/app/seq2seq \
			--mount type=bind,source=$(BASE_DIR)/data_all_in_$(SUFFIX_DATA)/,target=/app/data_all_in \
			--mount type=bind,source=$(BASE_DIR)/wandb,target=/app/wandb \
			eyuansu62/graphix-text-to-sql:v2 \
			/bin/bash -c "python seq2seq/run_seq2seq_train.py configs/train.json"

.PHONY: pre_process
pre_process: 
	mkdir -p -m 777 train_$(SUFFIX_DATA)
	mkdir -p -m 777 train_data_$(SUFFIX_DATA)
	mkdir -p -m 777 transformers_cache_$(SUFFIX_DATA)
	mkdir -p -m 777 wandb
	mkdir -p -m 777 data_all_in_$(SUFFIX_DATA)/data/output
	- chmod 777 data_all_in_$(SUFFIX_DATA)/*
	- chmod 777 seq2seq/*
	- chmod -R 777 data_all_in_$(SUFFIX_DATA)/data/*
	docker run \
			-it \
			--rm \
			--gpus '"device=$(AVAILABLE_GPUS)"'\
			--user 13011:13011 \
			-v $(BASE_DIR)/docker_patches/.bashrc:/home/toolkit/.bashrc \
			-v $(BASE_DIR)/docker_patches/.ssh:/home/toolkit/.ssh \
			--mount type=bind,source=$(BASE_DIR)/train_$(SUFFIX_DATA),target=/train \
			--mount type=bind,source=$(BASE_DIR)/train_data_$(SUFFIX_DATA),target=/train_data \
			--mount type=bind,source=$(BASE_DIR)/transformers_cache_$(SUFFIX_DATA),target=/transformers_cache \
			--mount type=bind,source=$(BASE_DIR)/configs,target=/app/configs \
			--mount type=bind,source=$(BASE_DIR)/configs/train.json,target=/app/configs/train.json \
			--mount type=bind,source=$(BASE_DIR)/seq2seq,target=/app/seq2seq \
			--mount type=bind,source=$(BASE_DIR)/data_all_in_$(SUFFIX_DATA),target=/app/data_all_in \
			--mount type=bind,source=$(BASE_DIR)/wandb,target=/app/wandb \
			eyuansu62/graphix-text-to-sql:v2 \
			/bin/bash -c "sh ./data_all_in/run/run_pre.sh"
		

.PHONY: eval
eval: 
	mkdir -p -m 777 eval_$(SUFFIX_MODEL)_$(SUFFIX_DATA)
	mkdir -p -m 777 transformers_cache_$(SUFFIX_MODEL)_$(SUFFIX_DATA)
	mkdir -p -m 777 wandb
	- chmod 777 data_all_in_$(SUFFIX_DATA)/*
	- chmod 777 train_db_id_$(SUFFIX_MODEL)/*
	docker run \
			-it \
			--rm \
			--gpus '"device=$(AVAILABLE_GPUS)"'\
			--cpus "40.0" \
			--user 13011:13011 \
			-v $(BASE_DIR)/docker_patches/.bashrc:/home/toolkit/.bashrc \
			-v $(BASE_DIR)/docker_patches/.ssh:/home/toolkit/.ssh \
			--mount type=bind,source=$(BASE_DIR)/eval_$(SUFFIX_MODEL)_$(SUFFIX_DATA),target=/eval \
			--mount type=bind,source=$(BASE_DIR)/transformers_cache_$(SUFFIX_MODEL)_$(SUFFIX_DATA),target=/transformers_cache \
			--mount type=bind,source=$(BASE_DIR)/train_db_id_$(SUFFIX_MODEL),target=/train_db_id \
			--mount type=bind,source=$(BASE_DIR)/configs,target=/app/configs \
			--mount type=bind,source=$(BASE_DIR)/configs/eval.json,target=/app/configs/eval.json \
			--mount type=bind,source=$(BASE_DIR)/seq2seq,target=/app/seq2seq \
			--mount type=bind,source=$(BASE_DIR)/data_all_in_$(SUFFIX_DATA)/,target=/app/data_all_in \
			--mount type=bind,source=$(BASE_DIR)/wandb,target=/app/wandb \
			eyuansu62/graphix-text-to-sql:v2 \
			/bin/bash -c "python seq2seq/run_seq2seq_eval.py configs/eval.json"
