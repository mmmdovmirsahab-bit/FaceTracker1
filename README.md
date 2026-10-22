# FaceTrack – Android üçün Center Stage alternativi

Ön kamera ilə video çəkir və üzünüzü avtomatik kadrın mərkəzində saxlayır (real kəsmə/reframing – çəkilən MP4 faylının özündə).

## Telefondan APK necə qurulur (kompüter lazım deyil)

1. Telefon brauzerində https://github.com saytına girin və hesab açın / daxil olun.
2. Sağ yuxarıda **+ → New repository** basın. Ad: `FaceTrack`. **Create repository**.
3. **creating a new file** linkinə basın. Fayl adı xanasına dəqiq bunu yazın: `.github/workflows/android.yml`
   (hər `/` yazanda qovluq yaranır). Gələn `android.yml` faylının məzmununu tam kopyalayıb yapışdırın. **Commit changes** basın.
4. Repository əsas səhifəsində **Add file → Upload files** seçin. `facetrack-project.zip` faylını seçin (ZİP-i açmayın!). **Commit changes** basın.
5. **Actions** tabına keçin. "Build FaceTrack APK" işləməyə başlayacaq (olmasa: onu seçin → **Run workflow**). ~5–8 dəqiqə çəkir.
6. Yaşıl ✓ olduqda iş qeydinə girin, aşağıda **Artifacts** bölməsində **FaceTrack-debug-apk** basıb yükləyin (ZİP gəlir).
7. Yüklənən ZİP-i telefonun **Files** tətbiqində açın, içindən `app-debug.apk` seçin, **Install** basın.
   Soruşsa "Bu mənbədən quraşdırmağa icazə ver" seçin.
8. Tətbiqi açın, **kamera** və **mikrofon** icazələrini verin.

Qırmızı ✗ olarsa: Actions → uğursuz iş → **build-log** artifact-ını yükləyin və xəta mətnini mənə göndərin.

## Necə işləyir
- CameraX **Preview** kadrları öz OpenGL `SurfaceTexture`-ə verir.
- **ImageAnalysis + ML Kit Face Detection** (cihazda, internetsiz) üzü tapır. Ən böyük üz seçilir, ML Kit tracking ID ilə izlənir.
- `FaceTracker` hədəf kəsimi hesablayır: sürətə məhdudiyyət, ölü zona, zoom histerezisi, aşağı-tezlik filtri.
- `GlRenderer` hər kadrı həmin kəsimlə **ekrana** (güzgü, selfi kimi) və **MediaCodec (H.264)** giriş səthinə (güzgüsüz) çəkir.
- Mikrofon `AudioRecord → AAC`, hər ikisi `MediaMuxer` ilə MP4 olur. Fayl `Movies/FaceTrack` qovluğuna (MediaStore) düşür, Qalereyada görünür.

## Test statusu (dürüst qeyd)
- Layihə **cihazda test olunmayıb və qurulma (build) yoxlanılmayıb** – yaradıldığı mühitdə internet və Android SDK yox idi. Yəni kod statik yoxlanılıb, amma real kompilyasiya GitHub Actions-da ilk dəfə olacaq. Kompilyasiya xətası çıxarsa, log-u göndərin, düzəldərik.
- Üz izləmə keyfiyyəti, audio/video sinxronu və fırlanma/güzgü istiqaməti real telefonda yoxlanmalıdır.

## Məlum məhdudiyyətlər
- Ön kamera adətən geniş bucaqlı deyil, ona görə izləmə **zoom** (Settings → Crop strength) hesabına işləyir. Zoom 1x olanda kəsiləcək yer yoxdur.
- Video vaxt möhürü kadrın GL-ə çatma vaxtından götürülür (sensor saatından yox) – ~bir neçə on ms sinxron sapması ola bilər.
- Analiz və preview eyni 16:9 nisbətini istəyir; cihaz fərqli nisbət qaytarsa, izləmədə kiçik sürüşmə ola bilər.
- Yalnız portret, yalnız ön kamera, yalnız debug APK (imzasız release üçün ayrıca keystore lazımdır; hazırda yaradılmayıb).
- `GlRenderer.MIRROR_RECORDING` sabitini `true` etsəniz, çəkilən video da güzgülü olar.

## Problemlər
- **Kamera qara**: tətbiqi bağlayıb yenidən açın; icazələri yoxlayın.
- **Səs yoxdur**: mikrofon icazəsi verin, Settings-də audio açıq olsun.
- **Video yazılmır**: Settings-də aşağı rezolyusiya (720p/480p) seçin.
- **Üz tərs/yan hərəkət edir**: mənə cihaz modelini göndərin.
