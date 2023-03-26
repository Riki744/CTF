# Needle in a Haystack
>You've obtained an ancient alien Datasphere, containing categorized and sorted recordings of every word in the forgotten intergalactic common language. Hidden within it is the password to a tomb, but the sphere has been worn with age and the search function no longer works, only playing random recordings. You don't have time to search through every recording - can you crack it open and extract the answer?

# Solve

In this challenge we are provided with one file, Opening file in Ghidra for inspecting main function

```
undefined8 main(void)

{
  int iVar1;
  time_t tVar2;
  int local_c;
  
  setbuf(stdout,(char *)0x0);
  printf("Hit enter to select a recording: ");
  getchar();
  tVar2 = time((time_t *)0x0);
  srand((uint)tVar2);
  for (local_c = 0; local_c < 3; local_c = local_c + 1) {
    putchar(0x2e);
    sleep(1);
  }
  iVar1 = rand();
  printf("\"%s\"\n",
         *(undefined8 *)(words + (long)(iVar1 + (int)((ulong)(long)iVar1 / 0xcb) * -0xcb) * 8));
  return 0;
}

```

It looks like there is processing of words , so going for words reference gives us the flag as it is stored in plaintext

```
001054f0 f5 33 10        addr       s_solguyum_001033f5                         = "solguyum"
                00 00 00 
                00 00
    001054f8 fe 33 10        addr       s_summeg_001033fe                           = "summeg"
                00 00 00 
                00 00
    00105500 05 34 10        addr       s_cyunkro_00103405                          = "cyunkro"
                00 00 00 
                00 00
    00105508 0d 34 10        addr       s_kroucaloc,_0010340d                       = "kroucaloc,"
                00 00 00 
                00 00
    00105510 18 34 10        addr       s_HTB{d1v1ng_1nt0_th3_d4tab4nk5}_00103418   = "HTB{d1v1ng_1nt0_th3_d4tab4nk5}"
                00 00 00 
                00 00
    00105518 37 34 10        addr       s_huarg_00103437                            = "huarg"
                00 00 00 
                00 00
    00105520 3d 34 10        addr       s_voremtc_0010343d                          = "voremtc"
                00 00 00 
                00 00
    00105528 45 34 10        addr       s_glyubyuur_00103445                        = "glyubyuur"
                00 00 00 
                00 00
    00105530 4f 34 10        addr       s_klesalo_0010344f                          = "klesalo"
                00 00 00 
```

Flag - `HTB{d1v1ng_1nt0_th3_d4tab4nk5}`

# Shortcut solve

Also all thing can be skipped by just simply checking strings on the file that was provided

![image](https://user-images.githubusercontent.com/85706972/227799318-19fd78a9-5feb-432b-8fc5-5e94bd491b1f.png)





