CyBrain
=======

Neural Networks in Cython, inspired by PyBrain, but 70x faster.

Check Comparison.py for a first approach. In this example we train a CyBrain network to solve the XOR problem. At the end we use PyBrain to solve the same problem. A speed comparison indicates that CyBrain is 70x faster.

XOR Example
===========

    from cybrain import Neuron, LogisticNeuron, Connection, Layer, Network, BiasUnit, Trainer, SoftMaxLayer
    import numpy as np
    from time import time
    nnet = Network()
    
    #TRUTH TABLE (DATA)
    X =     [[0.0,0.0]];     Y = [[0.0, 1.0]]
    X.append([1.0,0.0]); Y.append([1.0, 0.0])
    X.append([0.0,1.0]); Y.append([1.0, 0.0])
    X.append([1.0,1.0]); Y.append([0.0, 1.0])
    
    
    #CONVERT DATA TO NUMPY ARRAY
    X, Y = np.array(X), np.array(Y)
    
    Lin = Layer( 2, names =['1','2'])
    Lout = Layer( 2, LogisticNeuron, names= ['3', '4'] )
    Lhidden = Layer( 2, LogisticNeuron, names= ['5','6'] )
    bias = Layer( 1, BiasUnit, names=['b'] )
    
    #ADD LAYERS TO NETWORK
    nnet.addInputLayer(Lin)
    nnet.addOutputLayer(Lhidden)
    nnet.addLayer(Lout)
    nnet.addAutoInputLayer(bias)
    
    #CONNECT LAYERS
    Lin.connectTo(Lout)
    Lout.connectTo(Lhidden)
    bias.connectTo(Lhidden)
    bias.connectTo(Lout)
    
    #CREATE BATCH TRAINER
    rate = 0.1
    batch = Trainer( nnet, X, Y, rate )
    
    #TRAIN FOR 1000 EPOCHS
    t1 = time()
    batch.epochs(2000)
    print "Time CyBrain {}".format(time()-t1)
    
    #PRINT RESULTS
    for x in X:
        print "{} ==> {}".format( x, nnet.activateWith(x, return_value= True) )



Same Example in PyBrain
========================

    from pybrain.tools.shortcuts import buildNetwork
    from pybrain import LinearLayer, SigmoidLayer, FeedForwardNetwork, FullConnection, BiasUnit, SoftmaxLayer
    from pybrain.supervised.trainers.backprop import BackpropTrainer
    from pybrain.structure.modules.tanhlayer import TanhLayer
    from pybrain.datasets import SupervisedDataSet
    
    
    ds = SupervisedDataSet(2,1 )
    
    ds.addSample((0, 0), (0,))
    ds.addSample((0, 1), (1,))
    ds.addSample((1, 0), (1,))
    ds.addSample((1, 1), (0,))
    
    
    net = buildNetwork(2, 2, 1, bias=True, outputbias= True, hiddenclass=SigmoidLayer)
    trainer = BackpropTrainer(net, ds, learningrate= 0.1)
    
    t1 = time()
    trainer.trainEpochs(2000)
    print "Time PyBrain {}".format(time()-t1)
    
    #PRINT RESULTS
    for x in X:
        print "{} ==> {}".format( x, net.activate(x) )


Outputs
=======

    Time CyBrain 0.111654996872
    [ 0.  0.] ==> [['0.0365560102866', '0.963422516281']]
    [ 1.  0.] ==> [['0.951081842587', '0.0489475005295']]
    [ 0.  1.] ==> [['0.951928021684', '0.0481004789023']]
    [ 1.  1.] ==> [['0.0332036251855', '0.966776168457']]
    
    Time PyBrain 7.03572702408
    [ 0.  0.] ==> [  1.67662906e-08]
    [ 1.  0.] ==> [ 0.99999998]
    [ 0.  1.] ==> [ 0.99999998]
    [ 1.  1.] ==> [  7.30255101e-09]