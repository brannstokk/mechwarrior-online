# mechwarrior-online

Configuration files and scripts for Mechwarrior Online on Linux.

## mwo

Wrapper for launching MWO through Wine and ensuring CPU governor is
set to "performance".  The MWO/CryEngine/Wine combination is CPU-bound
on my machine.  For whatever reason, the default "ondemand" CPU
governor is not aggressive enough about prioritizing MWO.  Setting it
to "performance" increased my framerates to a playable number.

Also worth noting is that if I suspend my machine (at the time of
writing, Xeon ES-1650v3 @ 3.8 GHz) with `acpitool -s` with the
"performance" CPU governor enabled, upon waking it the CPU gets
throttled to 31% of its maximum frequency and will not exceed that
until I change the governor.

## mwo.module

[Environment Module](http://modules.sourceforge.net/) for MWO.  Sets
up Wine prefix, environment variables, etc.

## dxvk.conf

DXVK configuration file.  Never used this for any other games, but
some settings were suggested in [this
thread](https://mwomercs.com/forums/topic/268847-running-the-game-on-ubuntu-steam-play/page__view__findpost__p__6222284).

## tuning.cfg

It seems everyone uses this to invoke their user.cfg.

## user.cfg

I used some of the more aggressive configs for a while (dharper,
MrVaad, larsh, narcbait), but once I figured out the CPU governor and
upgraded my machine, I just use this.

Even though motion blur is disabled in the config, the only way to get
rid of it entirely is to disable anti-aliasing (AA) in the graphics
settings in-game.  See: <https://mwomercs.com/forums/topic/158148-what-does-motion-blur-even-do/page__view__findpost__p__3369270>

## Other notes

I built Wine 6.21 with wine-staging patches and the following
additional patch in order to be able to get MWO to work.

```diff
diff --git a/dlls/ntdll/path.c b/dlls/ntdll/path.c
index 47196067cd4..296a22e98d8 100644
--- a/dlls/ntdll/path.c
+++ b/dlls/ntdll/path.c
@@ -517,10 +517,9 @@ static ULONG get_full_path_helper(LPCWSTR name, LPWSTR buffer, ULONG size)

     RtlAcquirePebLock();

-    if (NtCurrentTeb()->Tib.SubSystemTib)  /* FIXME: hack */
-        cd = &((WIN16_SUBSYSTEM_TIB *)NtCurrentTeb()->Tib.SubSystemTib)->curdir.DosPath;
-    else
-        cd = &NtCurrentTeb()->Peb->ProcessParameters->CurrentDirectory.DosPath;
+	/* Mechwarrior Online */
+	/* https://mwomercs.com/forums/topic/268847-running-the-game-on-ubuntu-steam-play/page__st__20__p__6195387#entry6195387 */
+    cd = &NtCurrentTeb()->Peb->ProcessParameters->CurrentDirectory.DosPath;

     switch (RtlDetermineDosPathNameType_U(name))
     {
```

Apparently the Wine developers find this patch so abhorrent that they
refuse AppDB reports for MWO.  Since it sounds like it can cause
issues, I use this 6.21 Wine build exclusively for MWO.

See also:

<https://mwomercs.com/forums/topic/268847-running-the-game-on-ubuntu-steam-play/page__st__20__p__6195387#entry6195387>

<https://mwomercs.com/forums/topic/268847-running-the-game-on-ubuntu-steam-play/page__st__20__p__6222284#entry6222284>

