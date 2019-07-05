# -*- coding: utf-8 -*-
"""
Created on Sat Aug 18 12:43:13 2018
 
@author: Fabrizio
"""
 
import pandas as pd
import networkx as nx
from collections import defaultdict
 
graph = pd.read_csv('merchant frequency per card.csv', sep=';')
 
 
G = nx.Graph()
 
G.add_nodes_from(graph['Card Id'], bipartite='card')
G.add_nodes_from(graph['Card Acceptor Name And Location (group)'],
                 bipartite='merchant')
for _, edge in graph.iterrows():
    G.add_edge(edge['Card Id'], edge['Card Acceptor Name And Location (group)'],
               weight=edge['Number of Transactions'])
 
 
def shared_partition_nodes(G, node1, node2):
    # Check that the nodes belong to the same partition
    assert G.node[node1]['bipartite'] == G.node[node2]['bipartite']
 
    # Get neighbors of node 1: nbrs1
    nbrs1 = G.neighbors(node1)
    # Get neighbors of node 2: nbrs2
    nbrs2 = G.neighbors(node2)
 
    # Compute the overlap using set intersections
    overlap = set(nbrs1).intersection(nbrs2)
    return overlap
 
 
def card_similarity(G, card1, card2, merch_nodes):
    # Check that the nodes belong to the 'users' partition
    assert G.node[card1]['bipartite'] == 'card'
    assert G.node[card2]['bipartite'] == 'card'
 
    # Get the set of nodes shared between the two users
    shared_nodes = shared_partition_nodes(G, card1, card2)
 
    # Return the fraction of nodes in the projects partition
    return len(shared_nodes) / len(merch_nodes)
 
 
def most_similar_users(G, card, card_nodes, merch_nodes):
    # Data checks
    assert G.node[card]['bipartite'] == 'card'
 
    # Get other nodes from card partition
    card_nodes = set(card_nodes)
    card_nodes.remove(card)
 
    # Create the dictionary: similarities
    similarities = defaultdict(list)
    for n in card_nodes:
        similarity = card_similarity(G, card, n, merch_nodes)
        similarities[similarity].append(n)
 
    # Compute maximum similarity score: max_similarity
    max_similarity = max(similarities.keys())
 
    # Return list of users that share maximal similarity
    return similarities[max_similarity]
 
 
def recommend_repositories(G, from_card, to_card):
    # Get the set of repositories that from_user has contributed to
    from_merch = set(G.neighbors(from_card))
    # Get the set of repositories that to_user has contributed to
    to_merch = set(G.neighbors(to_card))
 
    # Identify repositories that the from_user is connected to that the to_user
    # is not connected to
    return from_merch.difference(to_merch)
