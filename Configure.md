# soot configure details

## 初始配置
    public static void sootInitialize(String apkFileLocation, String androidJar){
        soot.G.reset();

        Options.v().set_src_prec(Options.src_prec_apk);
        Options.v().set_soot_classpath(apkFileLocation + File.pathSeparator + androidJar);
        Options.v().set_process_dir(Collections.singletonList(apkFileLocation));
        Options.v().set_force_android_jar(androidJar);// 也可换成 Options.v().set_android_jars(xxx); 指定androidJar文件夹，如..//sdk//platforms
        Options.v().set_whole_program(true);
        Options.v().set_process_multiple_dex(true);
        Options.v().set_allow_phantom_refs(true);
        Options.v().setPhaseOption("cg.spark", "on");
        Options.v().set_output_format(Options.output_format_none);
        
        /*如果选择在指定文件夹下xxx输出jimple文件，该句改为
        Options.v().set_output_format(Options.output_format_jimple);
        Options.v().set_output_dir("xxx");*/
        
  
        Options.v().set_wrong_staticness(Options.wrong_staticness_fix);//防止有些应用staicness报错，https://github.com/soot-oss/soot/issues/1019

        Scene.v().loadNecessaryClasses();

        PackManager.v().runPacks();
        //PackManager.v().writeOutput();输出jimple文件
        
        /* 加载<clinit>部分,如果上面选择了输出jimple文件则不需要该步，因为PackManager.v().writeOutput()调用方法中已包含 */
        for (SootClass sc : Scene.v().getApplicationClasses()){
            if (!sc.isPhantom())
                ConstantValueToInitializerTransformer.v().transformClass(sc);
        }
    }
