# data description

Users provided 2 FID files, one in AMX mode, one with DMX mode.

A copy of each file is saved in each \_amx  and \_dmx folder.

For both nmrpipe folder, an FT2 file has been saved for every step in the following pipeline:

```
nmrPipe -in test.fid \
| nmrPipe -fn SP -off 0.5 -end 0.95 -pow 2 -elb 0.0 -glb 0.0 -c 0.5 \  # -> test1.ft2
| nmrPipe -fn ZF -zf 1 -auto \ # -> test2.ft2
| nmrPipe -fn FT -verb \ # -> test3.ft2
| nmrPipe -fn PS -p0 -41.5 -p1 -8.0 -di \ # -> test4.ft2
  -out test.ft2 -ov
```

For both nmrglue folder, an FT2 file has been saved for every step in the following pipeline:

```
rawdic,rawdata = ng.pipe.read("./filter/<FOLDER>/test0.fid") # step 0 

dicGLUE, data1 = ng.pipe_proc.sp(rawdic, rawdata, # step 1
                                off=0.5,
                                end=0.95,
                                pow=2,
                                c=0.5)
ng.pipe.write("./filter/<FOLDER>/test1.ft2", dicGLUE, data1)

dicGLUE, data2 = ng.pipe_proc.zf(dicGLUE, data1, # step 2
                                zf=1, 
                                auto=True)
ng.pipe.write("./filter/<FOLDER>/test2.ft2", dicGLUE, data2)

dicGLUE, data3 = ng.pipe_proc.ft(dicGLUE, data2) # step 3
ng.pipe.write("./filter/<FOLDER>/test3.ft2", dicGLUE, data3)

dicGLUE, data4 = ng.pipe_proc.ps(dicGLUE, data3,  # step 4
                                p0=-41.5, 
                                p1=-8.,)
ng.pipe.write("./filter/<FOLDER>/test4.ft2", dicGLUE, data4)
```

A comparison between nmrpipe and nmrglue transformed matrices has been evaluated by `numpy.allclose()` with different relative tolerance.

Results are reported below. Step is the matrix at each step: 0 is raw data readings. AMX is the comparison between pipelines for AMX FID, DMX for DMX FID. 

```
step	AMX	DMX 	 tolerance =1e-10
0	True	True	OK
1	False	False	!!
2	False	False	!!
3	False	False	!!
4	False	False	!!
step	AMX	DMX 	 tolerance =1e-08
0	True	True	OK
1	False	False	!!
2	False	False	!!
3	False	False	!!
4	False	False	!!
step	AMX	DMX 	 tolerance =1e-06
0	True	True	OK
1	True	True	OK
2	True	True	OK
3	False	False	!!
4	False	False	!!
step	AMX	DMX 	 tolerance =0.0001
0	True	True	OK
1	True	True	OK
2	True	True	OK
3	False	False	!!
4	False	False	!!
step	AMX	DMX 	 tolerance =0.01
0	True	True	OK
1	True	True	OK
2	True	True	OK
3	False	False	!!
4	False	False	!!
step	AMX	DMX 	 tolerance =1
0	True	True	OK
1	True	True	OK
2	True	True	OK
3	False	True	!!
4	False	False	!!
step	AMX	DMX 	 tolerance =100
0	True	True	OK
1	True	True	OK
2	True	True	OK
3	True	True	OK
4	False	False	!!
step	AMX	DMX 	 tolerance =10000
0	True	True	OK
1	True	True	OK
2	True	True	OK
3	True	True	OK
4	False	False	!!
```

# Observations:

* At any tolerance, the raw data readings are the same.
* For SP + ZF transformation, tolerance of difference between nmrglue and nmrpipe is between 1e-8 and 1e-10.
* for FT step, tolerance of difference is higher, and the matrices are not comparble anymore
* for PS step, matrices are different for any relative tolerance

