---
layout: post
title:  "custom progress bar"
date:   2023-09-13 09:00:00 +0900
categories: android
tags: [android, progressbarm custom]
image: /assets/img/post/custom_progress_bar.png
---

커스텀 프로그래스바 구현에 대한 일부 내용을 작성<br>
"CircularProgressIndicator"를 참고 하여 구현 요구 사항을 추가 하여 구현

## 요구사항
- 애니메이션처리 필요
- 프로그래스바 내부 cap round 처리
- change callback 처리 필요
- 그라데이션 처리 필요
- 프로그래스바 시작 위치 조절 필요

![img-description](/assets/img/post/custom_progress_bar.png)<br>
_요구 사항 이미지_

## 구현 순서
 - View를 Custom 하여 작업 진행<br>
background, progress, dot 순서로 onDraw통해 화면에 표현<br>
화면 사이즈를 기반으로 radius값을 구하고 drawArc를 통해 백그라운드, progress를 구현 하고 마지막으로 dot를 구현 하여 요구 사항과 같이 view를 생성


 - Progress 중앙의 텍스트는 xml에서 처리 하였으며 callback을 통해 텍스트를 업데이트 하는 방식으로 애니메이션을 연결<br> 
 animation callback listener 처리<br>
```kotlin
interface ProgressListener {
    fun onProgress(value: Float)
    fun onProgressEnd()
}

addListener(
    object : Animator.AnimatorListener {
        override fun onAnimationEnd(animation: Animator) {
            progressListener?.onProgressEnd()
        }

        override fun onAnimationEnd(animation: Animator, isReverse: Boolean) {
            super.onAnimationEnd(animation, isReverse)
            progressListener?.onProgressEnd()
        }
    }
)
```

## 문제 및 해결
 - gradient cap 색상 오류<br>
![img-description](/assets/img/post/custom_progress_bar_cap.png)<br>
_SweepGradient로 처리 하였을 때 시작 위치 gradient 처리 오류
> stackoverflow에서 비슷한 상황을 발견하여 동일 하게 처리하려고 했으나 기존 코드와 충돌 현상으로 인해 적용을 하지 못하였다. 임시 적으로 시작점에 point를 추가하여 요구사항과 동일 하게 처리 하였으나 추후 정리를 통해 코드 수정이 필요함.
 - 애니메이션 처리도중 뒤로 가기 이벤트, 앱이 백그라운드로 들어가면 앱의 강제 종료현상
> ValueAnimator를 생명주기에 따라 resume, pause, cancel을 처리하도록 추가

## 전체코드
```kotlin
class CircularProgress @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {
    private var progressPaint = Paint()
    private var progressBackgroundPaint = Paint()
    private var dotPaint = Paint()
    private var subDotPaint = Paint()
    private var tempPaint = Paint()
    private var startAngle = DEFAULT_PROGRESS_START_ANGLE
    private var sweepAngle = 0
    private var circleBounds = RectF()
    private var radius = 0f
    var isDotEnabled = true
        private set
    private var maxProgressValue = 100.0
    var progress = 0.0
        private set
    private var isAnimationEnabled = true
    private var isFillBackgroundEnabled = false
    private var isFillDotBackgroundEnabled = false

    @Direction
    private var direction = DIRECTION_CLOCKWISE
    private var progressAnimator: ValueAnimator? = null

    var progressListener: ProgressListener? = null

    init {
        init(context, attrs)
    }

    interface ProgressListener {
        fun onProgress(value: Float)
        fun onProgressEnd()
    }

    private fun init(context: Context, attrs: AttributeSet?) {
        var progressColor = Color.parseColor(DEFAULT_PROGRESS_COLOR)
        var progressBackgroundColor = Color.parseColor(DEFAULT_PROGRESS_BACKGROUND_COLOR)
        var progressStrokeWidth = dp2px(DEFAULT_STROKE_WIDTH_DP.toFloat())
        var progressBackgroundStrokeWidth = progressStrokeWidth
        val dotStrokeWidth = dp2px(DEFAULT_STROKE_WIDTH_DP.toFloat())
        var subDotWidth = 0
        var dotColor = progressColor
        var dotWidth = progressStrokeWidth.toFloat()
        var progressStrokeCap = Paint.Cap.ROUND
        if (attrs != null) {
            val a = context.obtainStyledAttributes(attrs, R.styleable.CircularProgress)
            progressColor =
                a.getColor(R.styleable.CircularProgress_progressColor, progressColor)
            progressBackgroundColor = a.getColor(
                R.styleable.CircularProgress_progressBackgroundColor,
                progressBackgroundColor
            )
            progressStrokeWidth = a.getDimensionPixelSize(
                R.styleable.CircularProgress_progressStrokeWidth, progressStrokeWidth
            )
            progressBackgroundStrokeWidth = a.getDimensionPixelSize(
                R.styleable.CircularProgress_progressBackgroundStrokeWidth,
                progressStrokeWidth
            )
            isDotEnabled = a.getBoolean(R.styleable.CircularProgress_drawDot, true)
            dotColor = a.getColor(R.styleable.CircularProgress_dotColor, progressColor)
            dotWidth = a.getDimensionPixelSize(
                R.styleable.CircularProgress_dotWidth, progressStrokeWidth
            ).toFloat()
            subDotWidth = a.getDimensionPixelSize(
                R.styleable.CircularProgress_subDotWidth,
                dotStrokeWidth
            )
            startAngle = a.getInt(
                R.styleable.CircularProgress_startAngle, DEFAULT_PROGRESS_START_ANGLE
            )
            if (startAngle < 0 || startAngle > 360) {
                startAngle = DEFAULT_PROGRESS_START_ANGLE
            }

            isAnimationEnabled =
                a.getBoolean(R.styleable.CircularProgress_enableProgressAnimation, true)
            isFillBackgroundEnabled =
                a.getBoolean(R.styleable.CircularProgress_fillBackground, false)
            isFillDotBackgroundEnabled =
                a.getBoolean(R.styleable.CircularProgress_fillDotBackground, false)
            direction = a.getInt(
                R.styleable.CircularProgress_direction, DIRECTION_COUNTERCLOCKWISE
            )
            val cap = a.getInt(R.styleable.CircularProgress_progressCap, CAP_ROUND)
            progressStrokeCap = if (cap == CAP_BUTT) {
                Paint.Cap.BUTT
            } else {
                Paint.Cap.ROUND
            }
            val gradientType = a.getColor(R.styleable.CircularProgress_gradientType, 0)
            if (gradientType != NO_GRADIENT) {
                val gradientColorEnd =
                    a.getColor(R.styleable.CircularProgress_gradientEndColor, -1)
                require(gradientColorEnd != -1) { "did you forget to specify gradientColorEnd?" }
                post { setGradient(gradientType, gradientColorEnd) }
            }
            a.recycle()
        }
        progressPaint.apply {
            strokeCap = progressStrokeCap
            strokeWidth = progressStrokeWidth.toFloat()
            style = Paint.Style.STROKE
            color = progressColor
            isAntiAlias = true
        }
        progressBackgroundPaint.apply {
            style = if (isFillBackgroundEnabled) Paint.Style.FILL_AND_STROKE else Paint.Style.STROKE
            strokeWidth = progressBackgroundStrokeWidth.toFloat()
            color = progressBackgroundColor
            isAntiAlias = true
        }
        dotPaint.apply {
            strokeCap = Paint.Cap.ROUND
            strokeWidth = dotWidth
            style = Paint.Style.FILL_AND_STROKE
            color = dotColor
            isAntiAlias = true
        }
        subDotPaint.apply {
            strokeCap = Paint.Cap.ROUND
            strokeWidth = subDotWidth.toFloat()
            style = Paint.Style.FILL_AND_STROKE
            color = progressBackgroundColor
            isAntiAlias = true
        }
        tempPaint.apply {
            strokeCap = Paint.Cap.ROUND
            strokeWidth = dotWidth
            style = Paint.Style.FILL_AND_STROKE
            color = progressColor
            isAntiAlias = true
        }
        circleBounds = RectF()
    }

    fun resume() {
        progressAnimator?.resume()
    }

    fun pause() {
        progressAnimator?.pause()
    }

    fun destroy() {
        progressAnimator?.cancel()
    }

    override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec)
        val paddingLeft = paddingLeft
        val paddingRight = paddingRight
        val paddingTop = paddingTop
        val paddingBottom = paddingBottom
        val measuredWidth = MeasureSpec.getSize(widthMeasureSpec)
        val measuredHeight = MeasureSpec.getSize(heightMeasureSpec)
        val widthMode = MeasureSpec.getMode(widthMeasureSpec)
        val heightMode = MeasureSpec.getMode(heightMeasureSpec)
        val dotWidth = dotPaint.strokeWidth
        val progressWidth = progressPaint.strokeWidth
        val progressBackgroundWidth = progressBackgroundPaint.strokeWidth
        val strokeSizeOffset = if (isDotEnabled) {
            dotWidth.coerceAtLeast(progressWidth.coerceAtLeast(progressBackgroundWidth))
        } else {
            progressWidth.coerceAtLeast(progressBackgroundWidth)
        }
        val desiredSize =
            strokeSizeOffset.toInt() + dp2px(DESIRED_WIDTH_DP.toFloat()) + (paddingBottom + paddingTop).coerceAtLeast(
                paddingLeft + paddingRight
            )

        // multiply by .1f to have an extra space for small padding between text and circle
        val finalWidth = when (widthMode) {
            MeasureSpec.EXACTLY -> measuredWidth
            MeasureSpec.AT_MOST -> desiredSize.coerceAtMost(measuredWidth)
            else -> desiredSize
        }
        val finalHeight = when (heightMode) {
            MeasureSpec.EXACTLY -> measuredHeight
            MeasureSpec.AT_MOST -> desiredSize.coerceAtMost(measuredHeight)
            else -> desiredSize
        }
        val widthWithoutPadding = finalWidth - paddingLeft - paddingRight
        val heightWithoutPadding = finalHeight - paddingTop - paddingBottom
        val smallestSide = heightWithoutPadding.coerceAtMost(widthWithoutPadding)
        setMeasuredDimension(smallestSide, smallestSide)
    }

    override fun onSizeChanged(w: Int, h: Int, oldw: Int, oldh: Int) {
        calculateBounds(w, h)
    }

    private fun calculateBounds(w: Int, h: Int) {
        radius = w / 2f
        val dotWidth = dotPaint.strokeWidth
        val progressWidth = progressPaint.strokeWidth
        val progressBackgroundWidth = progressBackgroundPaint.strokeWidth
        val strokeSizeOffset = if (isDotEnabled) {
            dotWidth.coerceAtLeast(progressWidth.coerceAtLeast(progressBackgroundWidth))
        } else {
            progressWidth.coerceAtLeast(progressBackgroundWidth)
            // to prevent progress or dot from drawing over the bounds
        }
        val halfOffset = strokeSizeOffset / 2f

        radius = circleBounds.apply {
            left = halfOffset
            top = halfOffset
            right = w - halfOffset
            bottom = h - halfOffset
        }.width() / 2f
    }

    override fun onDetachedFromWindow() {
        super.onDetachedFromWindow()
        progressAnimator?.cancel()
    }

    override fun onDraw(canvas: Canvas) {
        drawProgressBackground(canvas)
        drawProgress(canvas)
        if (isDotEnabled) drawDot(canvas)
    }

    private fun drawProgressBackground(canvas: Canvas) {
        canvas.drawArc(circleBounds, 0f, 360f, false, progressBackgroundPaint)
    }

    private fun drawProgress(canvas: Canvas) {
        canvas.drawArc(
            circleBounds,
            startAngle.toFloat(),
            sweepAngle.toFloat(), false, progressPaint
        )
        if (gradientType == SWEEP_GRADIENT) {
            val angleRadians = Math.toRadians((startAngle + 180).toDouble())
            val cos = cos(angleRadians).toFloat()
            val sin = sin(angleRadians).toFloat()
            val x = circleBounds.centerX() - radius * cos
            val y = circleBounds.centerY() - radius * sin
            canvas.drawPoint(x, y, tempPaint)
        }
    }

    private fun drawDot(canvas: Canvas) {
        val angleRadians = Math.toRadians((startAngle + sweepAngle + 180).toDouble())
        val cos = cos(angleRadians).toFloat()
        val sin = sin(angleRadians).toFloat()
        val x = circleBounds.centerX() - radius * cos
        val y = circleBounds.centerY() - radius * sin
        canvas.drawPoint(x, y, dotPaint)
        canvas.drawPoint(x, y, subDotPaint)
    }

    fun setCurrentProgress(currentProgress: Double) {
        if (currentProgress > maxProgressValue) {
            maxProgressValue = currentProgress
        }
        setProgress(currentProgress, maxProgressValue)
    }

    private fun degreeToRad(degree: Int): Double = degree * Math.PI / 180

    fun setProgress(current: Double, max: Double) {
        val finalAngle = if (direction == DIRECTION_COUNTERCLOCKWISE) {
            -(current / max * 360)
        } else {
            current / max * 360
        }
        val oldCurrentProgress = progress
        maxProgressValue = max
        progress = current.coerceAtMost(max)
        stopProgressAnimation()
        if (isAnimationEnabled) {
            startProgressAnimation(oldCurrentProgress, finalAngle)
        } else {
            sweepAngle = finalAngle.toInt()
            invalidate()
        }
    }

    private fun startProgressAnimation(oldCurrentProgress: Double, finalAngle: Double) {
        val angleProperty =
            PropertyValuesHolder.ofInt(PROPERTY_ANGLE, sweepAngle, finalAngle.toInt())
        progressAnimator = ValueAnimator.ofObject(
            object : TypeEvaluator<Double> {
                override fun evaluate(
                    fraction: Float, startValue: Double, endValue: Double
                ): Double {
                    return startValue + (endValue - startValue) * fraction
                }
            }, oldCurrentProgress, progress
        ).apply {
            duration = DEFAULT_ANIMATION_DURATION.toLong()
            setValues(angleProperty)
            interpolator = AccelerateDecelerateInterpolator()
            addUpdateListener { animation ->
                sweepAngle = animation.getAnimatedValue(PROPERTY_ANGLE) as Int

                progressListener?.onProgress(sweepAngle / 360f)
                invalidate()
            }
            addListener(object : Animator.AnimatorListener {
                override fun onAnimationStart(animation: Animator) {
                }

                override fun onAnimationEnd(animation: Animator) {
                    progressListener?.onProgressEnd()
                }

                override fun onAnimationCancel(animation: Animator) {
                    sweepAngle = finalAngle.toInt()
                    invalidate()
                    progressAnimator = null
                }

                override fun onAnimationRepeat(animation: Animator) {
                }

                override fun onAnimationEnd(animation: Animator, isReverse: Boolean) {
                    super.onAnimationEnd(animation, isReverse)
                    progressListener?.onProgressEnd()
                }
            })
        }
        progressAnimator?.start()
    }

    private fun stopProgressAnimation() {
        progressAnimator?.cancel()
    }

    private fun dp2px(dp: Float): Int {
        val metrics = resources.displayMetrics
        return TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, dp, metrics).toInt()
    }

    // calculates circle bounds, view size and requests invalidation
    private fun invalidateEverything() {
        calculateBounds(width, height)
        requestLayout()
        invalidate()
    }

    fun setProgressStrokeWidthDp(@Dimension strokeWidth: Int) {
        setProgressStrokeWidthPx(dp2px(strokeWidth.toFloat()))
    }

    fun setProgressStrokeWidthPx(@Dimension strokeWidth: Int) {
        progressPaint.strokeWidth = strokeWidth.toFloat()
        invalidateEverything()
    }

    fun setProgressBackgroundStrokeWidthDp(@Dimension strokeWidth: Int) {
        setProgressBackgroundStrokeWidthPx(dp2px(strokeWidth.toFloat()))
    }

    fun setProgressBackgroundStrokeWidthPx(@Dimension strokeWidth: Int) {
        progressBackgroundPaint.strokeWidth = strokeWidth.toFloat()
        invalidateEverything()
    }

    fun setShouldDrawDot(shouldDrawDot: Boolean) {
        isDotEnabled = shouldDrawDot
        if (dotPaint.strokeWidth > progressPaint.strokeWidth) {
            requestLayout()
            return
        }
        invalidate()
    }

    fun setDotWidthDp(@Dimension width: Int) {
        setDotWidthPx(dp2px(width.toFloat()))
    }

    fun setDotWidthPx(@Dimension width: Int) {
        dotPaint.strokeWidth = width.toFloat()
        invalidateEverything()
    }

    @get:ColorInt
    var progressColor: Int
        get() = progressPaint.color
        set(color) {
            progressPaint.color = color
            invalidate()
        }

    @get:ColorInt
    var progressBackgroundColor: Int
        get() = progressBackgroundPaint.color
        set(color) {
            progressBackgroundPaint.color = color
            invalidate()
        }
    val progressStrokeWidth: Float
        get() = progressPaint.strokeWidth
    val progressBackgroundStrokeWidth: Float
        get() = progressBackgroundPaint.strokeWidth

    @get:ColorInt
    var dotColor: Int
        get() = dotPaint.color
        set(color) {
            dotPaint.color = color
            invalidate()
        }
    val dotWidth: Float
        get() = dotPaint.strokeWidth
    var maxProgress: Double
        get() = maxProgressValue
        set(maxProgress) {
            maxProgressValue = maxProgress
            if (maxProgressValue < progress) {
                setCurrentProgress(maxProgress)
            }
            invalidate()
        }

    fun getStartAngle(): Int {
        return startAngle
    }

    fun setStartAngle(@IntRange(from = 0, to = 360) startAngle: Int) {
        this.startAngle = startAngle
        invalidate()
    }

    @Direction
    fun getDirection(): Int {
        return direction
    }

    fun setDirection(@Direction direction: Int) {
        this.direction = direction
        invalidate()
    }

    @get:Cap
    var progressStrokeCap: Int
        get() = if (progressPaint.strokeCap == Paint.Cap.ROUND) CAP_ROUND else CAP_BUTT
        set(cap) {
            val paintCap = if (cap == CAP_ROUND) Paint.Cap.ROUND else Paint.Cap.BUTT
            if (progressPaint.strokeCap != paintCap) {
                progressPaint.strokeCap = paintCap
                invalidate()
            }
        }

    fun setAnimationEnabled(enableAnimation: Boolean) {
        isAnimationEnabled = enableAnimation
        if (!enableAnimation) stopProgressAnimation()
    }

    fun isAnimationEnabled(): Boolean {
        return isAnimationEnabled
    }

    fun setFillBackgroundEnabled(fillBackgroundEnabled: Boolean) {
        if (fillBackgroundEnabled == isFillBackgroundEnabled) return
        isFillBackgroundEnabled = fillBackgroundEnabled
        val style = if (fillBackgroundEnabled) Paint.Style.FILL_AND_STROKE else Paint.Style.STROKE
        progressBackgroundPaint.style = style
        invalidate()
    }

    fun isFillBackgroundEnabled(): Boolean {
        return isFillBackgroundEnabled
    }

    fun setGradient(@GradientType type: Int, @ColorInt endColor: Int) {
        var gradient: Shader? = null
        val cx = width / 2f
        val cy = height / 2f
        val startColor = progressPaint.color
        when (type) {
            LINEAR_GRADIENT -> gradient = LinearGradient(
                0f,
                0f,
                width.toFloat(),
                height.toFloat(),
                startColor,
                endColor,
                Shader.TileMode.CLAMP
            )

            RADIAL_GRADIENT -> gradient =
                RadialGradient(cx, cy, cx, startColor, endColor, Shader.TileMode.MIRROR)

            SWEEP_GRADIENT -> gradient =
                SweepGradient(cx, cy, intArrayOf(startColor, endColor), null)
        }
        if (gradient != null) {
            val matrix = Matrix()
            matrix.postRotate(startAngle.toFloat(), cx, cy)
            gradient.setLocalMatrix(matrix)
        }
        progressPaint.shader = gradient
        invalidate()
    }

    @get:GradientType
    val gradientType: Int
        get() {
            val shader = progressPaint.shader
            var type = NO_GRADIENT
            when (shader) {
                is LinearGradient -> {
                    type = LINEAR_GRADIENT
                }

                is RadialGradient -> {
                    type = RADIAL_GRADIENT
                }

                is SweepGradient -> {
                    type = SWEEP_GRADIENT
                }
            }
            return type
        }

    @kotlin.annotation.Retention(AnnotationRetention.SOURCE)
    @IntDef(*[DIRECTION_CLOCKWISE, DIRECTION_COUNTERCLOCKWISE])
    annotation class Direction

    @kotlin.annotation.Retention(AnnotationRetention.SOURCE)
    @IntDef(*[CAP_ROUND, CAP_BUTT])
    annotation class Cap

    @kotlin.annotation.Retention(AnnotationRetention.SOURCE)
    @IntDef(*[NO_GRADIENT, LINEAR_GRADIENT, RADIAL_GRADIENT, SWEEP_GRADIENT])
    annotation class GradientType

    companion object {
        const val DIRECTION_CLOCKWISE = 0
        const val DIRECTION_COUNTERCLOCKWISE = 1
        const val CAP_ROUND = 0
        const val CAP_BUTT = 1
        const val NO_GRADIENT = 0
        const val LINEAR_GRADIENT = 1
        const val RADIAL_GRADIENT = 2
        const val SWEEP_GRADIENT = 3
        private const val DEFAULT_PROGRESS_START_ANGLE = 90
        private const val DESIRED_WIDTH_DP = 150
        private const val DEFAULT_PROGRESS_COLOR = "#C0A8FF"
        private const val DEFAULT_STROKE_WIDTH_DP = 8
        private const val DEFAULT_PROGRESS_BACKGROUND_COLOR = "#F1F1F1"
        private const val DEFAULT_ANIMATION_DURATION = 1000
        private const val PROPERTY_ANGLE = "angle"
    }
}
```

**참고**
- [gradient cap 색상 오류](https://stackoverflow.com/questions/59917009/how-to-include-stroke-cap-in-sweep-gradient)
- [CircularProgressIndicator](https://github.com/antonKozyriatskyi/CircularProgressIndicator)