

## Installation

### Manually Install

To run the code in this repo, you need to have `Python>=3.6`, `PyTorch>=1.6`, and `PyTorch-Geometric>=1.6.1`.
Please follow the installation instructions of [PyTorch-Geometric](https://github.com/rusty1s/pytorch_geometric) to install PyG.



## Datasets

If you have installed the latest version of PyG or DGL, you can use their built-in dataloaders to download and load the UPFD dataset.

If you install the project manually, you need to download the dataset (1.2GB) 
via the links below and
unzip the corresponding data under the `\data\{dataset_name}\raw\` directory, 
the `dataset_name` is `politifact` or `gossipcop`.

Google Drive: https://drive.google.com/drive/folders/1OslTX91kLEYIi2WBnwuFtXsVz5SS_XeR?usp=sharing

Baidu Disk: https://pan.baidu.com/s/1NFtuwzmpAezNcJzlSlduSw Password: fj43

The dataset includes fake&real news propagation networks on Twitter built according to fact-check information from
[Politifact](https://www.politifact.com/) and [Gossipcop](https://www.gossipcop.com/).
The news retweet graphs were originally extracted by [FakeNewsNet](https://github.com/KaiDMML/FakeNewsNet).
We crawled near 20 million historical tweets from users who participated in fake news propagation in FakeNewsNet to
generate node features in the dataset.

The statistics of the dataset is shown below:

| Data  | #Graphs  | #Fake News| #Total Nodes  | #Total Edges  | #Avg. Nodes per Graph  |
|-------|--------|--------|--------|--------|--------|
| Politifact | 314   |   157    |  41,054  | 40,740 |  131 |
| Gossipcop |  5464  |   2732   |  314,262  | 308,798  |  58  |


Due to the Twitter policy, we could not release the crawled user historical tweets publicly.
To get the corresponding Twitter user information, you can refer to news lists and the node_id-twitter_id mappings under `\data`.
Two `xxx_id_twitter_mapping.pkl` files include the dictionaries with the keys as the node_ids in the datasets and the values represent corresponding Twitter user_ids.
For the news node, its value represents news id in the [FakeNewsNet](https://github.com/KaiDMML/FakeNewsNet) datasets.
Similarly, two `xxx_id_time_mapping.pkl` files include the node_id to its corresponding Tweet timestamp mappings.
Note that the timestamp is in UNIX timestamp format. The news node doesn’t contain timestamp even in the original FakeNewsNet dataset, you can either retrieve it on Twitter or use its most recent retweet time as an approximation.
In the UPFD project, we use [Tweepy](https://www.tweepy.org/) and [Twitter Developer API](https://developer.twitter.com/en) to get the user information, the crawler code can be found at [\utils\twitter_crawler.py](https://github.com/safe-graph/GNN-FakeNews/blob/main/utils/twitter_crawler.py).

We incorporate four node feature types in the dataset, the 768-dimensional `bert` and 300-dimensional `spacy` features 
are encoded using pretrained [BERT](https://github.com/hanxiao/bert-as-service) and [spaCy](https://spacy.io/models/en#en_core_web_lg) word2vec, respectively.
The 10-dimensional `profile` feature is obtained from a Twitter account's profile.
You can refer to [profile_feature.py](https://github.com/safe-graph/GNN-FakeNews/blob/master/utils/profile_feature.py) for profile feature extraction.
The 310-dimensional `content` feature is composed of a 300-dimensional user comment word2vec (spaCy) embedding
plus a 10-dimensional profile feature.

Each graph is a hierarchical tree-structured graph where the root node represents the news, the leaf nodes are Twitter users who retweeted the root news.
A user node has an edge to the news node if he/she retweeted the news tweet. Two user nodes have an edge if one user retweeted the news tweet from the other user.
The following figure shows the UPFD framework including the dataset construction details 
You can refer to the [paper](https://arxiv.org/pdf/2005.00625.pdf) for more details about the dataset.

<p align="center">
    <br>
    <a href="https://github.com/safe-graph/GNN-FakeNews">
        <img src="https://github.com/safe-graph/GNN-FakeNews/blob/main/overview.png" width="1000"/>
    </a>
    <br>
<p>

## Models

All GNN-based fake news detection models are under the `\gnn_model` directory.
You can fine-tune each model according to arguments specified in the argparser of each model.
The implemented models are as follows:

* **[GNN-CL](https://arxiv.org/pdf/2007.03316.pdf)**: Han, Yi, Shanika Karunasekera, and Christopher Leckie. "Graph neural networks with continual learning for fake news detection from social media." arXiv preprint arXiv:2007.03316 (2020).
* **[GCNFN](https://arxiv.org/pdf/1902.06673.pdf)**: Monti, Federico, Fabrizio Frasca, Davide Eynard, Damon Mannion, and Michael M. Bronstein. "Fake news detection on social media using geometric deep learning." arXiv preprint arXiv:1902.06673 (2019).
* **[BiGCN](https://arxiv.org/pdf/2001.06362.pdf)**: Bian, Tian, Xi Xiao, Tingyang Xu, Peilin Zhao, Wenbing Huang, Yu Rong, and Junzhou Huang. "Rumor detection on social media with bi-directional graph convolutional networks." In Proceedings of the AAAI Conference on Artificial Intelligence, vol. 34, no. 01, pp. 549-556. 2020.
* **[UPFD-GCN](https://arxiv.org/pdf/1609.02907.pdf)**: Kipf, Thomas N., and Max Welling. "Semi-supervised classification with graph convolutional networks." arXiv preprint arXiv:1609.02907 (2016).
* **[UPFD-GAT](https://arxiv.org/pdf/1710.10903.pdf)**: Veličković, Petar, Guillem Cucurull, Arantxa Casanova, Adriana Romero, Pietro Lio, and Yoshua Bengio. "Graph attention networks." arXiv preprint arXiv:1710.10903 (2017).
* **[UPFD-SAGE](https://cs.stanford.edu/people/jure/pubs/graphsage-nips17.pdf)**: Hamilton, William L., Rex Ying, and Jure Leskovec. "Inductive representation learning on large graphs." arXiv preprint arXiv:1706.02216 (2017).

Since the UPFD framework is built upon the [PyG](https://github.com/rusty1s/pytorch_geometric), you can easily try other graph classification models
like [GIN](https://github.com/rusty1s/pytorch_geometric/blob/master/examples/mutag_gin.py) and [HGP-SL](https://github.com/cszhangzhen/HGP-SL)
under our dataset.


